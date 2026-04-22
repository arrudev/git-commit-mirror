# 🪞 Git Commit Mirror Automático

Este é um script de automação que utiliza **Global Git Hooks** para espelhar commits de qualquer projeto na sua máquina local diretamente para o repositório do seu perfil no GitHub.

Sempre que você fizer um commit em projetos locais, um commit vazio será enviado em background para o seu repositório principal, mantendo o seu gráfico de contribuições ativo.

## 🚀 Como funciona

O script utiliza o hook global `post-commit` do Git. Assim que um commit é finalizado em qualquer repositório local, o script roda em subshell (background) e cria um commit vazio no seu repositório de perfil, fazendo o push automaticamente.

## 🛠️ Instalação (Windows, Mac e Linux)

**⚠️ Nota para usuários de Windows:** Recomenda-se executar todos os comandos abaixo utilizando o **Git Bash** para garantir a compatibilidade dos comandos Linux/Unix.

### 1. Clone o repositório do seu perfil

O primeiro passo é garantir que o repositório especial do seu perfil (aquele que tem exatamente o seu nome de usuário e contém o seu `README.md` principal) esteja clonado na sua máquina.

Se você ainda não tem ele clonado abra o seu terminal e rode:

```bash
# Navegue até a pasta onde você guarda seus projetos
cd ~/SeusProjetos

# Clone o repositório (substitua SEU_USUARIO pelo seu usuário do GitHub)
git clone git@github.com:SEU_USUARIO/SEU_USUARIO.git

# Acesse a pasta para garantir que está tudo certo
cd SEU_USUARIO
```

### 2. Crie o diretório global de Hooks

Agora, vamos configurar o Git para aceitar hooks globais (válidos para todos os repositórios da máquina). No terminal, execute:

```bash
# Crie uma pasta oculta para salvar os templates de hooks
mkdir -p ~/.git-templates/hooks

# Configure o Git para usar essa pasta como diretório padrão de hooks
git config --global core.hooksPath ~/.git-templates/hooks
```

### 3. Configurando o Hook

Crie e abra o arquivo post-commit no seu editor de código (neste exemplo, usando o VS Code):

```bash
code ~/.git-templates/hooks/post-commit
```

Com o arquivo aberto, cole exatamente o script abaixo. Atenção ao passo 2 do script, onde você deve inserir o caminho correto do seu computador:

```bash
#!/bin/bash

# 1. Trava de segurança para evitar Loop Infinito
if [ "$MIRROR_IS_RUNNING" == "1" ]; then
    exit 0
fi

# 2. CONFIGURAÇÃO: Caminho absoluto do seu repositório de perfil
# IMPORTANTE: Use o formato do Git Bash (/c/Users/...)
PROFILE_REPO="/c/Users/USUARIO_WINDOWS/Documents/SEU_USUARIO"

# 3. Execução em segundo plano (background)
(
    cd "$PROFILE_REPO" || exit
    CURRENT_DATE=$(date +'%Y-%m-%d %H:%M:%S')

    # Realiza o commit injetando a trava de segurança
    MIRROR_IS_RUNNING=1 git commit --allow-empty -m "mirror: atividade detectada - $CURRENT_DATE"

    # Push silencioso
    git push origin main > /dev/null 2>&1
) &
```

Salve e feche o arquivo. De volta ao terminal, dê permissão de execução para o script:

```bash
chmod +x ~/.git-templates/hooks/post-commit
```

**💡 Dica de Manutenção**: Caso a branch principal do seu repositório de perfil se chame `master` em vez de `main`, lembre-se de alterar a linha `git push origin main` no script acima para `git push origin master`

## 🛑 Desativando

Se desejar interromper a automação e voltar ao comportamento padrão do Git sem hooks globais, basta abrir o terminal e rodar:

```bash
git config --global --unset core.hooksPath
```
