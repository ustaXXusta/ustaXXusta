name: Profile cards

on:
  push:
    branches: [main]
  schedule:
    - cron: "0 3 * * *"   # daily refresh
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6

      - name: Stats card
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: stats
          options: username=${{ github.repository_owner }}&show_icons=true&include_all_commits=true&theme=tokyonight&hide_border=true&bg_color=0D1117&title_color=7DD3FC&icon_color=A855F7&text_color=C9D1D9
          path: profile/stats.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Top languages card
        uses: stats-organization/github-readme-stats-action@v2
        with:
          card: top-langs
          options: username=${{ github.repository_owner }}&layout=compact&langs_count=8&hide=makefile,ampl&theme=tokyonight&hide_border=true&bg_color=0D1117&title_color=7DD3FC&text_color=C9D1D9
          path: profile/top-langs.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Contribution snake
        uses: Platane/snk/svg-only@v3
        with:
          github_user_name: ${{ github.repository_owner }}
          outputs: |
            profile/snake.svg
            profile/snake-dark.svg?palette=github-dark&color_snake=#7dd3fc

      - name: Commit cards
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add profile/*.svg
          git commit -m "Update profile cards" || exit 0
          git push
