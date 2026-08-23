# SuperApp Interview Arena

Plataforma de estudo (curso-game) para entrevista sênior de **Android + IA agêntica**, em português.

- **23 assuntos** no banco (Android a fundo, Kotlin, IA agêntica, SDD, Claude/Skills/MCP, AWS, DevOps, testes, WCAG, Java…), cada um com **aula + exemplos de código + quiz difícil** (comparativo / pegadinha / cenário real) — 131 questões.
- **Vagas**: crie uma vaga, escolha os assuntos do banco, estude e feche na **prova final (Boss)**.
- **Áudio da aula (TTS)**: ouça cada aula com a voz do dispositivo — voz **masculina/feminina** selecionável, velocidade e download de **audiobook offline** da vaga.
- **Progresso** salvo no navegador (localStorage) + **exportar/importar** backup.

Tudo em um único `index.html` — HTML/CSS/JS puro, sem build e sem dependências externas (só Google Fonts).

## Rodar local

Abra `index.html` no navegador. Só isso.

## Deploy

### Netlify (recomendado)
1. **New site from Git** → conecte este repositório.
2. Build command: *(vazio)* · Publish directory: `.`
3. Deploy. (config já em `netlify.toml`.)

Ou por CLI, na pasta do projeto:
```bash
netlify login
netlify deploy --prod --dir .
```

Ou **arraste a pasta** em https://app.netlify.com/drop (sem login).

### GitHub Pages
Settings → Pages → Branch `main` / root. Servirá `index.html`.
