+++
title = "Git Pages"
tags = ["git","pages"]
date = "2026-02-15"
toc = false
+++  


{{< notice info >}}
«Git. Если ты сделал commit, но забыл сделать push - ты теперь единственный хранитель мудрости»
{{< /notice >}}


> Установка Hugo Git Pages with Themes
>
> 1. `hugo new site portfolio`
> 2. `cd portfolio`
> 3. `git init`
> 4. `git branch -m main`
> 5. `git add .`
> 6. `git commit -m "create structure for Git Pages"`
> 7. `git branch develop`
> 8. `git checkout develop`
> 9. `git submodule add https://github.com/vaga/hugo-theme-m10c.git themes/m10c`
> 10. `git add .`
> 11. `git commit -m "install theme m10c"`



<div style="color: #0096b1; font-weight: normal; margin-bottom: 5px;">Активация эмодзи</div>


# 1. Локально создаем сайт и инициализируем репозиторий
hugo new site my-site && cd my-site && git init

# 2. Скачиваем тему как Git-подмодуль и активируем её
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> hugo.toml

# 3. Настраиваем GitHub Actions для автоматического деплоя
mkdir -p .github/workflows && cat << 'EOF' > .github/workflows/hugo.yml
name: Deploy Hugo site to Pages
on:
  push:
    branches: ["main"]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true
          fetch-depth: 0
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true
      - name: Build
        run: hugo --gc --minify
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
EOF

# 4. Публикуем код (замени <юзернейм> на свой логин GitHub)
# Важно: В настройках репозитория (Settings -> Pages) выбери ветку gh-pages для деплоя
git add .
git commit -m "Initial Hugo setup"
git branch -M main
git remote add origin https://github.com/<юзернейм>/<юзернейм>.github.io.git
git push -u origin main




> `enableEmoji = true - прописывается в верхнем блоке config.tolm`