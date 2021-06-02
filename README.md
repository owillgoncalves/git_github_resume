# GIT E GITHUB

## INTRODUÇÃO / LÓGICA DO VERSIONAMENTO

Evitar perda de controle entre versões de arquivos e alterações realizadas entre eles.

O Git foi criado por Linus Torvalds, para versionar o kernel do Linux.

GitHub

- Controle de Versão 
- Armazenamento em nuvem
- Trabalho em equipe
- Melhorar seu código
- Reconhecimento
___
## Navegação básica no terminal

GUI (Graphic User Interface) x CLI (Command Line Interface)

### O que vamos aprender?

- Mudar de diretório
- Listar diretórios
- Criar pastas/arquivos
- Deletas pastas/arquivos

### Listar diretórios e arquivos no diretório atual

```bash
$ ls
```

### Mudar diretório

```bash
$ cd /path
```

### Limpar terminal (ou CTRL + L)

```bash
$ clear
```

### Autocomplete (TAB)

```bash
$ cd P(ath)
```

### Criar diretório

```bash
$ mkdir directory-name
```

### Criar arquivo

```bash
$ echo hello > hello.txt
```

### Remover diretório (-rf => recursive / force)

```bash
$ rm -rf directory-name/
```
___
## Entendendo como o GIT funciona por baixo dos panos

### SHA1
- Secure Hash Algorithm.
- Conjunto de funções hash criptográficas projetadas pela NSA (Agência de Segurança Nacional dos EUA)
- A encriptação gera um conjunto de 40 caracteres para cada versão de um arquivo

Exemplo:

```bash
$ echo hello > hello.txt
$ openssl sha1 hello.txt
    SHA1(hello.txt)= f572d396fae9206628714fb2ce00f72e94f2258f

$ echo hello2 > hello.txt
$ openssl sha1 hello.txt
    SHA1(hello.txt)= 0d2aae7d156d796b97ae11b4dba506a55f54a75e

$ echo hello > hello.txt
$ openssl sha1 hello.txt
    SHA1(hello.txt)= f572d396fae9206628714fb2ce00f72e94f2258f
```

### Objetos fundamentais

#### BLOBS

- Objeto com metadados. Ex: `blob 6\0hello`, sendo:
    - Bloco básico de composição para versão de um conteúdo
    - blob: tipo do objeto
    - 6: tamanho do conteúdo em bytes
    - \0: caractere nulo / separador entre cabeçalho e conteúdo
    - hello: conteúdo
    - É gerado um SHA1, mas com valor diferente do método anterior, já que o blob possui mais dados e, consequentemente, é diferente do arquivo "cru"

```bash
$ git hash-object hello.txt
ce013625030ba8dba906f756967f9e9ca394464a
```

#### TREE

- Conjunto de blobs e/ou outras trees. Organiza, em uma única estrutura, um conjunto de alterações/versões.
- `tree <tamanho> \0 ... [blob <sha1> <nome-do-arquivo>] [...] [trees...] [blobs...]`
- É gerado um SHA1 que será alterado a partir das alterações/versões de qualquer uma das dependências.

    - TREE
        - README => BLOB
        - file.txt => BLOB
        - /lib => TREE
            - other.txt => BLOB

#### COMMIT

- Objeto final que dá sentido a alteração, contendo:
    - O SHA1 da TREE com as alterações
    - O SHA1 do último commit antes dele (quando houver)
    - O autor desse commit
    - A mensagem que dá sentido a alteração
    - Timestamp do momento do commit
- O SHA1 desse commit é o hash de toda a alteração e também é influenciado por qualquer alteração, tanto nas dependências da TREE, como nos outros metadados

### Sistema distribuído

Como os commits são independentes e possuem informações únicas, mesmo que duas pessoas pessoas fizerem a mesma alteração, a partir de um mesmo ponto, os SHA1 serão distintos e portanto, serão duas versões diferentes. Isso garante que não aconteça colisão e duplicidade entre versões.

### Segurança

Se um repositório principal for compartilhado por um grupo de pessoas e ele acabar sendo perdido, qualquer membro desse grupo que tiver a versão atualizada desse repositório poderá reintegrá-lo a origem, mantendo todo o histórico de alterações e versões do projeto.
___
## PRIMEIROS COMANDOS COM GIT

### git config [--global] [value]

Adiciona configurações ao utilitário git local, como e-mail e nome do autor dos commits:

```bash
$ git config --global user.email "seuemail@dominio.com"
$ git config --global user.name "Nome Sobrenome"
```

Para confirmar a atribuição, use os seguintes comandos:

```bash
$ git config --get user.email
seuemail@dominio.com

$ git config --get user.name
Nome Sobrenome
```

### git init

Inicia um repositório vazio na pasta atual. É criada uma pasta oculta chamada '.git/', onde a estrutura do repositório é armazenada. É possível listar o conteúdo dessa pasta, pelo próprio terminal:

```bash
$ git init
$ cd .git
$ ls
HEAD  config  description  hooks/  info/  objects/  refs/
```

### git add [file name | * | .]

Adiciona um ou mais arquivos que tiveram seu conteúdo alterado desde o último commit

```bash
$ git add [arquivo]
```

### git commit

Cria uma anotação com todas as alterações e informações descritar no tópico anterior (Objetos Fundamentais > COMMIT). Usando a flag `-m` seguida de uma `"mensagem"`, damos sentido ao conteúdo "commitado".

```bash
$ git commit -m "Descrição das alterações"
```

### git status

Exibe o status dos arquivos do repositório, na branch atual

```bash
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   README.md

no changes added to commit (use "git add" and/or "git commit -a")
```

### git restore [file]

Descarta as alterações de um arquivo

### git restore --staged [file]

Retorna com o arquivo de 'staged' (após `git add`) para 'unstaged'

### git rm [flag] [file]

Remove um arquivo do repositório

___
## CICLO DE VIDA DOS ARQUIVOS NO GIT

    UNTRACKED       ------------------------TRACKED-----------------------
        |               |                       |                      |
        |         [UNMODIFIED]              [MODIFIED]              [STAGED]
        |               |                       |                      |
        | git add ---------------------------------------------------> |
        |               |                       |                      |                                                                    
        |               | Arquivo editado ----> |                      |
        |               |                       |                      |
        |               |                       | git add -----------> |
        |               | <-------- git restore | <-..restore --staged |
        | <----- git rm |                       |                      |
        |               |                       |                      |
        |               | <-------------------------------- git commit |
___
## REPOSITÓRIOS

                                                        SERVIDOR------------|
                                                        |                   |
                                                        |[REMOTE REPOSITORY]|
                                                        |-------------------|

    AMBIENTE DE DESENVOLVIMENTO---------------------------------------------|
    |                                                                       |
    |[WORKING DIRECTORY]          [STAGING AREA]         [LOCAL REPOSITORY] |
    |-----------------------------------------------------------------------|


Working Directory: Arquivos untracked, unmodified e modified

Staging Area: Arquivo adicionado - git add (arquivo)

Local Repository: "Snapshot" dos arquivos commitados, que voltam a estar disponíveis, após versionados, no working directory

