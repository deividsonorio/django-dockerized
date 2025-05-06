# Django + Docker
1.0.0

## Tecnologias utilizadas

### Back-end

- [PostgreSQL](https://www.postgresql.org/)
- [Redis](https://redis.io/)
- [Celery](https://github.com/celery/celery)

### Front-end

- [esbuild](https://esbuild.github.io/)
- [TailwindCSS](https://tailwindcss.com/)
- [Heroicons](https://heroicons.com/)

## Adicionados

- **Pacotes e extensões**:
    - *[gunicorn](https://gunicorn.org/)* para um servidor de aplicativos em desenvolvimento e produção
    - *[whitenoise](https://github.com/evansd/whitenoise)* para servir arquivos estáticos
    - *[django-debug-toolbar](https://github.com/jazzband/django-debug-toolbar)* para exibir informações do app
- **Linting e formatação**:
    - *[flake8](https://github.com/PyCQA/flake8)* usado para limpar a base de código
    - *[isort](https://github.com/PyCQA/isort)* usado para classificar automaticamente as importações do Python
    - *[black](https://github.com/psf/black)* é usado para formatar a base de código
- **Django apps**:
    - Adicionado  o app `pages` para renderizar uma página inicial
    - Adicionado o app `up` para fornecer algumas páginas de verificação de saúde do sistema
- **Config**:
    - Log para STDOUT para que o Docker possa consumir e lidar com a saída do log
    - Extraídos um monte de definições de configuração em variáveis de ambiente
    - Renomeado o diretório do projeto para o nome personalizado para `config/`
    - o arquivo `sréc/config/settings.py` e o `.env` contém as configurações de todos os ambientes
- **Assets do Front-end**:
    - `assets/` contém todos os seus CSS, JS, imagens, fontes, etc. e é gerenciado por esbuild
    - Páginas `502.html` e `maintenance.html` personalizadas
    - Favicons gerados usando as melhores práticas modernas
- **Padrões do Django que foram alterados**:
    - Uso do Redis como back-end de cache padrão
    - Use cookies assinados como back-end da sessão
    - `public/` é o diretório estático onde o Django servirá arquivos estáticos
    - `public_collected/` é onde `collectstatic` irá gravar seus arquivos
    - Um arquivo `requirements-lock.txt` foi introduzido usando `pip3`. O gerenciamento deste arquivo é totalmente automatizado pelos comandos encontrados no arquivo `run`.

## Rodado o app

Você precisa ter o [Docker instalado](https://docs.docker.com/get-docker/).

Você também precisará habilitar o suporte ao Docker Compose v2 se estiver usando o Docker Desktop. 
No Linux nativo sem Docker Desktop você pode [instalar como um plugin para o Docker](https://docs.docker.com/compose/install/linux/).
Este projeto usa o Docker Compose e só funcionará com a versão v2 2.20.2+.

#### Iniciando o projeto

Clone o repositório do projeto.

#### Copie o .env de exemplo para um novo:

```sh
cp .env.example .env
```

#### Construa tudo:

*A primeira vez que você executar o projeto isso poderá levar de 5 a 10 minutos, dependendo da velocidade de conexão com a internet e especificações de hardware do computador. 
Serão baixadas algumas imagens Docker e feito o build das dependências Python + Yarn.*

```sh
docker compose up --build
```

#### Setup inicial do banco:

```sh
./run manage migrate
```

#### Visualizar no browser:

Vá para o endereço <http://localhost:8000> no navegador.

<br>

### Outras ferramentas

#### Executando o linter na base de código:

```sh
# Você não deve obter nenhuma saída (isso significa que tudo está operacional).
./run lint
```

#### Organizando as importações do Python na base de código:

```sh
# Você não deve obter nenhuma saída (isso significa que tudo está operacional).
./run format:imports
```

#### Formatando a base de código:

```sh
# Você deverá ver que tudo permanece inalterado (já está tudo formatado).
./run format
```

*Também há o comando `./run quality` que rodas os 3 comandos acima juntos.*

#### Executando o conjunto de testes:

```sh
# Você deverá ver todos os testes aprovados. Os warnings normalmente são aceitáveis.
./run manage test
```

#### Parando tudo:

```sh
# Pare os contêineres e remova alguns recursos relacionados ao Docker associados a este projeto.
docker compose down
```

Você pode começar tudo de novo com `docker compose up` e ao contrário do primeiro
tempo, deve levar apenas alguns segundos.

### `.env`

Este arquivo é ignorado pelo controle de versão, portanto nunca será commitado. Há um
monte de variáveis de ambiente definidas nele, que controlam certas opções e
comportamentos do aplicativo. Tudo está documentado lá.

Sinta-se à vontade para adicionar novas variáveis conforme necessário. É aqui que você deve colocar todas as chaves e senhas, bem como a configuração que pode mudar dependendo do seu
ambiente (desenvolvimento, CI, produção, etc.).

### `run`

Você pode rodar `./run` para ver uma lista de comandos e cada um deles tem uma
documentação no arquivo `run`.

É um shell script que possui diversas funções definidas para interagir
com o projeto. É basicamente um `Makefile`, mas com menos limitações.

Ele é útil para executar vários comandos do Docker porque às vezes esses
os comandos podem ser um pouco longos para serem digitados. Sinta-se à vontade para adicionar 
comandos úteis.

*Se você se cansar de digitar `./run` você sempre pode criar um alias com
`alias run=./run` em seu `~/.bash_aliases` ou arquivo equivalente. Então você estará
capaz de executar `run` em vez de `./run`.*

## Executando um script para automatizar a renomeação do projeto

O nome do aplicativo é `hello` agora, mas é provável que seu novo aplicativo precise de um novo.
Como o aplicativo já foi criado, precisaremos localizar/substituir em um
algumas variantes da string "hello" e atualize alguns recursos relacionados ao Docker.

Este arquivo faz todo o trabalho pesado para você. Tudo que você precisa fazer é executar o script abaixo.

#### Execute o script rename-project incluído neste repositório:

```sh
# O script leva 2 argumentos.
#
# O primeiro é a versão minúscula do nome do aplicativo, como meuapp ou
# meu_app, dependendo da sua preferência.
#
# O segundo é usado para o nome do módulo do seu aplicativo. Por exemplo, se você usou
# meuapp ou meu_app para o primeiro argumento seria melhor se você usasse MeuApp aqui.
bin/rename-project meuapp MeuApp
```

O script vai:

- Remover quaisquer recursos do Docker do seu projeto atual
- Executar uma série de ações de localizar/substituir
- Opcionalmente, inicializar um novo repositório git para você

*Depois você pode excluir o script pois seu único propósito é auxiliar a
a mudar o nome deste projeto sem depender de nada complicado como
ferramentas geradoras de projetos ou dependências de terceiros.*

#### Iniciando e configure o projeto:

Isso não demorará tanto quanto antes porque o Docker pode reutilizar a maioria das coisas do cache.
Também precisamos configurar o banco de dados, pois um novo será criado pelo Docker.

```sh
docker compose up --build

# Em seguida, em um segundo terminal, quando estiver instalado e pronto.
./run manage migrate
```

#### Verificação de sanidade para garantir que os testes ainda passem:

É sempre uma boa ideia certificar-se de que as coisas estão funcionando antes
adicionando alterações personalizadas.

```sh
# Você pode executar no mesmo terminal de antes.
./run quality
./run manage test
```

Se tudo passar agora você pode opcionalmente `git add -A && git commit -m
"Commit inicial"` e comece a personalizar o sistema.

## Atualizando dependências

Let's say you've customized your app and it's time to make a change to your
`requirements.txt` or `package.json` file.

Without Docker you'd normally run `pip3 install -r requirements.txt` or `yarn
install`. With Docker it's basically the same thing and since these commands
are in our `Dockerfile` we can get away with doing a `docker compose build` but
don't run that just yet.

You can also access `pip3` and `yarn` in Docker with `./run pip3` and `./run
yarn` after you've upped the project.

#### Em desenvolvimento

Você pode executar `./run pip3:outdated` ou `./run yarn:outdated` para obter uma lista de
dependências desatualizadas com base no que você tem instalado. Uma vez que você
descobriu o que você deseja atualizar, faça essas atualizações em seu
Arquivo `requirements.txt` e/ou `assets/package.json`.

Para atualizar dependências você pode executar `./run deps:install`. Isto irá
construir uma nova imagem com quaisquer novas dependências e também assegurar que qualquer atualização do arquivo lock
seja copiada da sua imagem para o seu código.

Além disso, você pode executar `./run deps:install --no-build` para copiar apenas 
as atualizações do arquivo lock sem reconstruir uma imagem.

Os arquivos lock garantem que as versões exatas
de cada pacote que você possui (incluindo dependências de dependências) sejam usados
na próxima vez que você construir o projeto.

Você nunca deve modificar os arquivos lock manualmente. Adicione suas dependências no
arquivo `requirements.txt` e suas dependências JavaScript em `assets/package.json` e 
execute o(s) comando(s) `./run` mencionado(s) anteriormente.

#### Em CI:

Você poderá executar `docker compose build`, pois ele usará qualquer arquivo lock existente. 
Você também pode conferir o pipeline de teste de CI completo no arquivo run
na função `ci:test`.

#### Em produção:

Vocẽ pode utilizar imagens pré buildadas de seu registry docker, 
mas se você decidir construir suas imagens Docker diretamente no
seu servidor, você pode executar `docker compose build` como parte de sua pipeline.

## Saiba mais sobre Docker e Django

### Documentação oficial

- <https://docs.docker.com/>
- <https://docs.djangoproject.com/en/5.1/>