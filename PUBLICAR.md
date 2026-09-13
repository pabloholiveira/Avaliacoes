# Como publicar no GitHub Pages

Hospedagem gratuita, sem custo recorrente. Você faz isso **uma vez**; depois é só
subir o JSON de cada cliente novo.

> Não há git nem GitHub CLI instalados nesta máquina, então o roteiro abaixo usa
> só o navegador. Funciona bem para o volume deste projeto (arquivos pequenos,
> enviados de vez em quando).

---

## 1. Criar o repositório

1. Entre em <https://github.com> e crie uma conta, se ainda não tiver.
2. Clique em **New repository**.
3. **Repository name:** `avaliacoes`
4. Marque **Public**. *(GitHub Pages em conta gratuita só funciona em repositório público.)*
5. **Não** marque "Add a README file".
6. **Create repository**.

## 2. Enviar os arquivos

Na página do repositório recém-criado, clique em **uploading an existing file**.

Arraste estes itens da pasta `Landingpage_avl`:

| Item | Vai pro repositório? |
|---|---|
| `index.html` | **Sim** — é a landing |
| `clientes/` (a pasta inteira) | **Sim** — os dados de cada cliente |
| `admin.html` | Opcional — veja a observação abaixo |
| `gerar_qrcode.py` | Opcional — não atrapalha |
| `PUBLICAR.md` | Opcional |
| `qrcodes/` | **Não** — são gerados, não precisam subir |
| `*.bak.*` | **Não** — backups do editor |
| `carteira-*.json` | **Nunca** — contém a sua carteira inteira de clientes |

Escreva qualquer coisa em *Commit changes* e confirme.

## 3. Ligar o GitHub Pages

1. No repositório: **Settings** → **Pages** (menu da esquerda).
2. Em *Source*, escolha **Deploy from a branch**.
3. Branch: **main**, pasta: **/ (root)**. **Save**.
4. Espere 1 a 2 minutos. A URL aparece no topo da mesma página, no formato:

```
https://SEU-USUARIO.github.io/avaliacoes/
```

## 4. Configurar o painel

1. Abra o `admin.html`.
2. Clique na engrenagem (**Configurações gerais**).
3. Cole a URL do passo 3 em **URL base da hospedagem** e salve.

Pronto. O aviso âmbar some, os links passam a apontar para o endereço real e o
botão **Baixar QR** é liberado.

---

## Rotina para cada cliente novo

1. No painel: preencher os dados → **Salvar alterações** → **Baixar JSON**.
2. No GitHub: entrar na pasta `clientes/` → **Add file** → **Upload files** →
   arrastar o JSON → confirmar.
3. Esperar ~1 minuto e conferir `https://SEU-USUARIO.github.io/avaliacoes/?c=SLUG`.
4. Só então **Baixar QR** e mandar a placa para impressão.

**Nunca imprima uma placa antes de conferir o link no navegador.** A placa é
física; o arquivo é de graça.

---

## Duas coisas que você precisa saber

**Os arquivos em `clientes/` são públicos.** Qualquer pessoa pode abrir
`https://SEU-USUARIO.github.io/avaliacoes/clientes/barbearia-do-ze.json` e ler o
WhatsApp do cliente. Isso é inerente ao modelo sem backend: a landing precisa
buscar esse arquivo no navegador do visitante. Na prática é o mesmo número que o
negócio já divulga na fachada e no Google — mas é bom você saber antes de
prometer confidencialidade a alguém.

**Publicar o `admin.html` é opcional.** Ele funciona 100% no seu computador via
`python -m http.server`. Se publicar, qualquer um que descobrir o endereço abre o
painel — mas não vê seus clientes, porque a lista fica no `localStorage` do
*seu* navegador, não no servidor. O ganho é poder editar de outra máquina; o
custo é expor a ferramenta. Para começar, recomendo **não** publicar.

---

## Rodando o painel localmente

```powershell
cd "c:\Users\henrique.silva\Desktop\Landingpage_avl"
python -m http.server 8080
```

Depois abra <http://localhost:8080/admin.html>.

Precisa ser por servidor, não abrindo o arquivo direto: a landing usa `fetch`
para ler `clientes/*.json`, e o protocolo `file://` bloqueia isso.

---

## Backup

O painel guarda a carteira no `localStorage` do navegador. Limpar os dados do
navegador ou trocar de máquina **apaga tudo**.

Em **Configurações gerais → Backup da carteira**:

- **Exportar clientes** baixa `carteira-AAAA-MM-DD.json` com todos eles.
- **Importar backup** restaura o arquivo, mesclando com o que já existe
  (mostra quantos serão criados e quantos substituídos antes de confirmar).

Exporte depois de cadastrar cada cliente novo. O arquivo de backup **não** deve
ir para o GitHub — já está no `.gitignore`.
