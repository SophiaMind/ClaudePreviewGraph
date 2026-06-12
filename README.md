# Visualizador de Grafo de Dependências · SophiaMind
 
![Build Status](https://img.shields.io/badge/build-passing-brightgreen)
![License](https://img.shields.io/badge/license-MIT-blue.svg)
![Version](https://img.shields.io/badge/version-2.1.0-blue)
![Stack](https://img.shields.io/badge/stack-HTML5%20%2B%20JS%20%2B%20D3.js-orange)
![Pages](https://img.shields.io/badge/deploy-GitHub%20Pages-222)
 
## Introdução
 
O **Visualizador de Grafo de Dependências** é uma ferramenta web que lê os grafos
gerados pelo nosso sistema configurado com o **Claude Code**
(`.claude/doc/graph.json`) e os exibe de forma interativa, para que engenheiros e
demais membros da equipe possam visualizar com mais clareza as **dependências e os
relacionamentos entre cada camada do projeto** (domain, application,
infrastructure e entrypoint).
 
Em vez de varrer o codebase com `grep` para entender o impacto de uma mudança, a
equipe abre a página, busca o módulo e enxerga na hora **quem depende dele, o que
ele usa e quem implementa cada interface** — com o layout em anéis concêntricos
refletindo a regra de dependência da Clean Architecture: qualquer seta apontando
"para fora" do centro salta aos olhos como violação de camada.
 
Desenvolvido pela [SophiaMind](https://sophiamind.com.br) como uma página estática
única (`index.html`), sem build, sem backend e sem instalação de dependências.
 
## Funcionalidades
 
- **Modo Arquitetura**: anéis concêntricos por camada — domain no centro,
  entrypoint na borda — evidenciando violações da regra de dependência
- **Modo Orgânico**: layout de força livre (force-directed) para explorar clusters
- **Painel de detalhes** por módulo: descrição, arquivo, exports, "Depende de" e
  "Usado por", com navegação clicável entre dependências
- **Filtros** por camada (domain, application, infrastructure, entrypoint, shared,
  config) e por tipo de relação (`uses`, `depends_on`, `implements`, `extends`)
- **Busca instantânea** por id do módulo, caminho do arquivo, exports ou descrição
  (atalho `/`)
- **Exportação** do grafo em **PNG de alta resolução** (~2400px) e **SVG vetorial**
- **Carregamento flexível**: fetch automático do `graph.json`, upload de arquivo,
  drag & drop ou colar o JSON direto na página
- Interfaces (`type: interface`) renderizadas como nós vazados, diferenciando
  contratos de implementações
- Tooltip com descrição ao passar o mouse e destaque da vizinhança do nó
- Responsivo (desktop e mobile) e com suporte a `prefers-reduced-motion`
## Pré-requisitos
 
Nenhuma instalação é necessária — é uma página estática. Você só precisa de:
 
- Um navegador moderno (Chrome, Firefox, Edge ou Safari)
- Conexão com a internet no primeiro carregamento (D3.js e fontes via CDN)
- Um arquivo `graph.json` no formato definido em [`GRAPH_SPEC.md`](GRAPH_SPEC.md),
  gerado e mantido pelo Claude Code no repositório do sistema
Para servir localmente (opcional), qualquer servidor estático resolve — por
exemplo, Python 3 já instalado na máquina.
 
## Instalação
 
Siga as etapas abaixo para rodar o visualizador em sua máquina local:
 
1. Clone o repositório:
```bash
    git clone https://github.com/sophiamind/ClaudePreviewGraph.git
```
2. Navegue até o diretório do projeto:
```bash
    cd ClaudePreviewGraph
```
3. Coloque o grafo do seu projeto ao lado do `index.html` (ou use o exemplo embutido):
```bash
    cp /caminho/do/projeto/.claude/doc/graph.json ./graph.json
```
4. Sirva a pasta com um servidor estático (necessário para o fetch do JSON funcionar):
```bash
    python -m http.server 8000
```
 
## Uso
 
Após iniciar o servidor, acesse o visualizador em http://localhost:8000.
 
A página carrega o grafo nesta ordem de prioridade:
 
1. `graph.json` ao lado do `index.html`
2. `.claude/doc/graph.json` (se o repositório inteiro for publicado)
3. Exemplo embutido de demonstração
Também é possível **arrastar e soltar** um `graph.json` em qualquer ponto da
página, usar o botão **Carregar JSON** ou **Colar** o conteúdo diretamente.
 
### Publicação no GitHub Pages
 
1. Suba `index.html` e o `graph.json` para a branch publicada (ou pasta `docs/`)
2. Em **Settings → Pages**, aponte para a branch/pasta
3. Acesse `https://seu-usuario.github.io/seu-repo/`
4. Para atualizar o grafo, basta substituir o `graph.json` — nenhuma alteração no
   HTML é necessária
## Exemplos de Uso
 
Consultas rápidas no terminal sobre o mesmo `graph.json` exibido pela página
(úteis em revisões de código e análises de impacto):
 
```bash
# Quem depende de um módulo? (impacto de uma mudança)
python3 -c "
import json
g = json.load(open('.claude/doc/graph.json'))
alvo = 'domain/recording/entity/recording_task'
for e in g['edges']:
    if e['to'] == alvo:
        print(f\"  {e['from']}  ({e['type']})\")
"
```
 
```bash
# Quem implementa uma interface?
python3 -c "
import json
g = json.load(open('.claude/doc/graph.json'))
iface = 'application/recording/port/hls_downloader'
for e in g['edges']:
    if e['to'] == iface and e['type'] == 'implements':
        print(f\"  {e['from']}\")
"
```
 
No visualizador, o equivalente é clicar no módulo e consultar as seções
**"Usado por"** e **"Depende de"** do painel de detalhes.
 
## Contribuindo
 
Contribuições são bem-vindas! Por favor, siga as diretrizes em
[CONTRIBUTING.md](CONTRIBUTING.md) para fazer um pull request. Pontos de atenção:
 
- O projeto é intencionalmente um único `index.html` sem etapa de build —
  mantenha assim
- O grafo segue o contrato de [`GRAPH_SPEC.md`](GRAPH_SPEC.md); mudanças no formato
  devem ser refletidas na especificação e no parser (`parseGraph`)
- Lembre o invariante do grafo: **a atualização do `graph.json` entra no mesmo
  commit da mudança de código**
## Licença
 
Distribuído sob a licença MIT. Veja [LICENSE](LICENSE) para mais informações.
 
## Autores
 
**SophiaMind** — Desenvolvimento e manutenção — [sophiamind.com.br](https://sophiamind.com.br)
 
## Agradecimentos
 
- [D3.js](https://d3js.org/) — simulação de força e renderização do grafo
- [Claude Code](https://www.anthropic.com/claude-code) — geração e manutenção
  automática do `graph.json` a cada commit do sistema
- [Google Fonts](https://fonts.google.com/) — Space Grotesk e JetBrains Mono
- A todos os engenheiros da equipe que usam e dão feedback sobre a ferramenta
