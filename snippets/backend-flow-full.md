# Flow backend complet (référence Q&A)

Ce fichier contient le YAML complet du flow `preproduction-flow.yml`
référencé sur la slide 6.6. À garder ouvert dans un onglet pendant
la Q&A au cas où on te demande les détails.

```yaml
name: preproduction flow

# Pre-release tags, e.g. v1.2.3-rc.1
on:
  push:
    tags:
      - v*.*.*-rc.*

jobs:
  notify-slack-deploy:
    name: Notify Slack deploy
    runs-on: ubuntu-latest
    steps:
      - name: Notify Slack deploy
        uses: wealthcome-SAS/actions/send-slack-message@stable
        with:
          slack-token: ${{ secrets.SLACK_BOT_TOKEN }}
          channel-id: ${{ secrets.SLACK_DEPLOY_CHANNEL_ID }}
          title: ":rocket: Préproduction Backend en cours de déploiement"
          body: |
            *Version:* <https://github.com/${{ github.repository }}/releases/tag/${{ github.ref_name }}|${{ github.ref_name }}>
            *Repository:* <https://github.com/${{ github.repository }}|${{ github.repository }}>
          url: "https://github.com/${{ github.repository }}/releases/tag/${{ github.ref_name }}"

  publish:
    uses: wealthcome-SAS/actions/.github/workflows/publish.yml@stable
    with:
      tag: |
        ${{ vars.APPLI_REGISTRY_URL }}/backend
        ${{ vars.APPLI_REGISTRY_URL }}/backend:${{ github.ref_name }}
        ${{ vars.AWS_REGISTRY_URL_PREPRODUCTION }}/backend
        ${{ vars.AWS_REGISTRY_URL_PREPRODUCTION }}/backend:${{ github.ref_name }}
      deploy-to-scaleway: true
    secrets:
      GIT_PAT_PULL: ${{ secrets.GIT_PAT_PULL }}
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID_PREPRODUCTION }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY_PREPRODUCTION }}
      SCALEWAY_REGISTRY_URL: ${{ secrets.APPLI_REGISTRY_URL }}
      SCALEWAY_REGISTRY_USERNAME: ${{ secrets.APPLI_REGISTRY_USER }}
      SCALEWAY_REGISTRY_PASSWORD: ${{ secrets.APPLI_REGISTRY_PASSWORD }}

  migrate:
    name: Migrate
    needs: publish
    uses: ./.github/workflows/migrate.yml
    with:
      runner: preproduction-services
      environment: preproduction-aws
      concurrency_group: backend-preproduction-aws-migrate
    secrets:
      GIT_PAT_PULL: ${{ secrets.GIT_PAT_PULL }}
      DATABASE_URL: ${{ secrets.DATABASE_URL }}

  migrate-scaleway:
    name: Migrate (Scaleway)
    needs: publish
    uses: ./.github/workflows/migrate.yml
    with:
      runner: preproduction-services
      environment: preproduction
      concurrency_group: backend-preproduction-migrate
    secrets:
      GIT_PAT_PULL: ${{ secrets.GIT_PAT_PULL }}
      DATABASE_URL: ${{ secrets.DATABASE_URL }}

  deploy-aws:
    concurrency: backend-preproduction-aws
    needs: migrate
    uses: ./.github/workflows/aws-deploy.yml
    # ... (voir README du repo pour le full YAML)

  deploy-cgp-aws:
    concurrency: backend-cgp-preproduction-aws
    needs: migrate
    # ...

  deploy:
    concurrency: backend-preproduction
    needs: migrate-scaleway
    # ...

  deploy-cgp:
    concurrency: backend-cgp-preproduction
    needs: migrate-scaleway
    # ...

  notify-slack-success:
    name: Notify Slack success
    runs-on: ubuntu-latest
    needs: [deploy-aws, deploy]
    if: success()
    # ...

  notify-slack-failure:
    name: Notify Slack failure
    runs-on: ubuntu-latest
    needs: [deploy-aws, deploy]
    if: failure()
    # ...

  notify-slack-cancelled:
    name: Notify Slack cancelled
    runs-on: ubuntu-latest
    needs: [deploy-aws, deploy]
    if: cancelled()
    # ...
```

## Points à mentionner en Q&A si demandé

- **Pourquoi 2 clouds (AWS + Scaleway) ?** Clients sur les deux.
- **Pourquoi CGP séparé ?** Environnement iso mais différent pour les CGP.
- **Pourquoi des `needs:` imbriqués ?** Migration doit passer avant deploy. Jamais de parallélisme hasardeux.
- **Pourquoi 3 jobs de notif (success/failure/cancelled) ?** GitHub Actions ne propage pas les statuts négatifs par défaut sur les jobs qui attendent d'autres jobs ; on force une notif dans tous les cas.
