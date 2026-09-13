# Como publicar no GitHub Pages

Hospedagem gratuita, sem custo recorrente. A configuração abaixo é feita **uma
vez**; depois, cada cliente novo é um `git push`.

Esta pasta já é um repositório git. Requisitos, todos já presentes nesta máquina:

| Ferramenta | Verificar com | Situação |
|---|---|---|
| git | `git --version` | 2.54.0 (Xcode Command Line Tools) |
| GitHub CLI | `gh auth status` | autenticado como `pabloholiveira` |
| Python 3 | `python3 --version` | usado para rodar o painel local |

---

## 1. Criar o repositório e publicar

Na pasta do projeto:

```bash
cd ~/Desktop/Landingpage_avl
gh repo create avaliacoes --public --source=. --remote=origin --push
```

Isso cria o repositório no GitHub, liga esta pasta a ele e envia tudo, em um
comando só.

**Por que público:** o GitHub Pages em conta gratuita só serve repositórios
públicos. Se a sua conta for Pro, `--private` também funciona.

<details>
<summary>Alternativa pelo navegador, sem o <code>gh</code></summary>

1. Em <https://github.com/new>: nome `avaliacoes`, **Public**, **sem** README.
2. **Create repository**.
3. De volta ao terminal:

```bash
git remote add origin https://github.com/pabloholiveira/avaliacoes.git
git push -u origin main
```
</details>

## 2. Ligar o GitHub Pages

```bash
gh repo view --web
```

No repositório: **Settings** → **Pages** → *Source*: **Deploy from a branch** →
branch **main**, pasta **/ (root)** → **Save**.

Espere 1 a 2 minutos. A URL aparece no topo da mesma página:

```
https://pabloholiveira.github.io/avaliacoes/
```

## 3. Configurar o painel

1. Abra o `admin.html` (veja *Rodando o painel* mais abaixo).
2. Clique na engrenagem (**Configurações gerais**).
3. Cole a URL do passo 2 em **URL base da hospedagem** e salve.

Pronto. O aviso âmbar some, os links passam a apontar para o endereço real e o
botão **Baixar QR** é liberado.

---

## Rotina para cada cliente novo

1. **No painel:** preencher os dados → **Salvar alterações** → **Baixar JSON**.
2. **No Finder:** mover o arquivo baixado de `~/Downloads` para a pasta
   `clientes/` do projeto.
3. **No terminal:**

   ```bash
   cd ~/Desktop/Landingpage_avl
   git add clientes/
   git commit -m "Adiciona cliente barbearia-do-ze"
   git push
   ```

4. Esperar ~1 minuto e **abrir no navegador**
   `https://pabloholiveira.github.io/avaliacoes/?c=SLUG`.
5. Só então **Baixar QR** e mandar a placa para impressão.

**Nunca imprima uma placa antes de conferir o link no navegador.** A placa é
física; o arquivo é de graça.

O passo 4 é rápido de checar: se o slug não estiver publicado, a página mostra
**"Página não encontrada"** em letras grandes. Ela nunca finge ser outro
negócio — foi feita assim justamente para esse erro não chegar impresso.

### Publicando vários clientes de uma vez

```bash
git add clientes/ && git commit -m "Adiciona 3 clientes" && git push
```

### Ver o que ainda não foi publicado

```bash
git status --short          # arquivos alterados ou novos
git log origin/main..main   # commits feitos mas não enviados
```

---

## Duas coisas que você precisa saber

**Os arquivos em `clientes/` são públicos.** Qualquer pessoa pode abrir
`https://pabloholiveira.github.io/avaliacoes/clientes/barbearia-do-ze.json` e ler
o WhatsApp do cliente. Isso é inerente ao modelo sem backend: a landing precisa
buscar esse arquivo no navegador do visitante. Na prática é o mesmo número que o
negócio já divulga na fachada e no Google — mas é bom você saber antes de
prometer confidencialidade a alguém.

**O `admin.html` não vai para o repositório.** Ele está no `.gitignore`: é
ferramenta interna e roda 100% no seu computador. Publicado, qualquer um que
descobrisse o endereço abriria o painel — não veria seus clientes, porque a
lista fica no `localStorage` do *seu* navegador e não no servidor, mas teria o
gerador em mãos.

O custo dessa escolha é que **o git não guarda o `admin.html`**. Se precisar
editá-lo de outra máquina, ou se quiser versioná-lo, remova a linha `admin.html`
do `.gitignore` — sabendo que ele passa a ficar público junto com o resto.

---

## Rodando o painel localmente

```bash
cd ~/Desktop/Landingpage_avl
python3 -m http.server 8080
```

Depois abra <http://localhost:8080/admin.html>. Para encerrar, `Ctrl+C`.

Precisa ser por servidor, não abrindo o arquivo direto: a landing usa `fetch`
para ler `clientes/*.json`, e o protocolo `file://` bloqueia isso.

---

## Backup

São duas coisas diferentes, e só uma delas o git protege:

| O quê | Onde vive | Protegido por |
|---|---|---|
| Landing e JSONs dos clientes | nesta pasta | **git** — `git push` |
| `admin.html` | só nesta pasta | **nada** — está no `.gitignore` |
| Carteira do painel | `localStorage` do navegador | **só o export manual** |

A carteira é a lista que aparece na barra lateral do painel. Limpar os dados do
navegador ou trocar de máquina **apaga tudo** — e o git não vê nada disso.

Em **Configurações gerais → Backup da carteira**:

- **Exportar clientes** baixa `carteira-AAAA-MM-DD.json` com todos eles.
- **Importar backup** restaura o arquivo, mesclando com o que já existe
  (mostra quantos serão criados e quantos substituídos antes de confirmar).

Exporte depois de cadastrar cada cliente novo. O arquivo de backup **não** deve
ir para o GitHub — já está no `.gitignore`, junto com `qrcodes/`, os `*.bak.*` e
o `.DS_Store`.
