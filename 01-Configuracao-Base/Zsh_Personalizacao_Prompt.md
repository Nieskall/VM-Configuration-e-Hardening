# 🎨 Zsh: Personalização Definitiva do Shell (Prompt e Título)

Este documento registra o processo de *troubleshooting* e a configuração final do prompt (`$PROMPT`) e do título da janela no Zsh, eliminando erros de interpretação de variáveis e códigos de escape.

## 1. Solução 1: Prompt de Comando Colorido

### 1.1. Resolução do Shell Padrão

Para que as configurações de personalização fossem carregadas em cada sessão, o **Zsh** foi definido como o shell padrão do usuário, garantindo que o `~/.zshrc` fosse lido na inicialização.

* **Comando:** `chsh -s /bin/zsh`
* **Ação:** Fechar e reabrir o terminal (ou fazer logoff/login).

### 1.2. Configuração do `$PROMPT`

A string de `$PROMPT` foi definida para exibir `usuario@host [diretorio] :` com cores, utilizando a sintaxe nativa do Zsh (`%n`, `%m`, `%c` e `%F{cor}`).

* **Linha Adicionada ao `~/.zshrc`:**
    ```bash
    PROMPT='%F{red}%n@%m %F{cyan}[%c]%F{yellow}:%F{reset} '
    ```

## 2. Solução 2: Título da Janela Automático (O Desafio Final)

Definir o título da janela (aba do terminal) com o formato `usuario@host: ~/diretório` exigiu uma função que envia um código de escape (`\e]2;...`) ao emulador de terminal.

### 2.1. Correção do Erro `print: bad option: -e`

O erro `print: bad option: -e` foi encontrado ao tentar usar o comando `print -ne` dentro da função de título.
* **Ação:** A solução foi utilizar o comando **`print -Pn`** (Process Prompt Escapes e No Newline), que é a sintaxe correta do Zsh para expandir variáveis e emitir códigos de escape.

### 2.2. Função de Título da Janela

A função abaixo foi adicionada ao `~/.zshrc` para garantir que o título da aba se atualize a cada novo prompt (`precmd`).

```bash
# Adicionado ao ~/.zshrc para definir o título da aba
setopt extended_glob
case "$TERM" in
    xterm*|rxvt*|Eterm|aterm|kterm|gnome*|konsole*)
        # print -Pn usa as flags -P (expandir prompt) e -n (sem nova linha)
        precmd() { print -Pn "\e]2;%n@%m: %~\a" }
        ;;
esac

### 3. Lição Final
A personalização do shell depende da sintaxe correta do shell ativo. No Zsh, a solução elegante e funcional para o prompt e o título da janela é usar os recursos nativos de prompt: $PROMPT para a linha de comando e print -Pn dentro da função precmd() para a barra de títulos.
