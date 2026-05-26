+++
title = "Git Pages"
tags = ["git","pages"]
date = "2026-02-15"
+++  

<blockquote style="border-left: 5px solid #0096b1; padding: 10px; background: #222; color: #ccc;">
    <div style="display: flex; align-items: center; gap: 10px; margin-bottom: 10px;">
        <span style="font-size: 1.5em;">💡</span>
        <i style="line-height: 1.6;">«Git. Если ты сделал commit, но забыл сделать push - ты теперь единственный хранитель мудрости»</i>
    </div>
    <div style="text-align: right; margin-top: 15px; color: #0096b1;">
        <b><i>— John Dou</i></b>
    </div>
</blockquote>

> <div style="color: #0096b1; font-weight: normal; margin-bottom: 5px;">Установка Hugo Git Pages with Themes</div>
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
>
> <div style="color: #0096b1; font-weight: normal; margin-bottom: 5px;">Активация эмодзи</div>
>
> `enableEmoji = true - прописывается в верхнем блоке config.tolm`