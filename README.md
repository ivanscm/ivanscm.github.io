# Serve
```
hugo server -D
```
# Build
```
hugo --gc --minify
```
# Deploy
Деплой автоматический: пуш в `main` запускает GitHub Actions (`.github/workflows/hugo.yaml`),
который собирает сайт и публикует на GitHub Pages: https://ivanscm.github.io/
