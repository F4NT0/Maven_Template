# Template de Projeto com Maven

### Criando um Projeto Maven

Utilize o seguinte comando no terminal:

* `mvn archetype:generate`
* **groupId** = nome do grupo que está trabalhando
* **artifactId** = nome do artefato especifico, sendo o projeto em si
* **package** = vai ser o nome do pacote, mesmo nome do artifactId

### Testando os testes no Maven

Utilize o seguinte comando no terminal:

* `mvn test`

Serve para rodar os testes unitários desenvolvidos no programa

Pacotes para poder rodar testes automatizados, colocar no **pom.xml**

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.6.2</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-engine</artifactId>
    <version>5.6.2</version>
    <scope>test</scope>
</dependency>
```

Quando feito modificações no maven recompile o pom.xml: `mvn clean install`


### Compilando o Projeto

Adicione o seguinte Plugin no **pom.xml**

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <configuration>
        <mainClass>com.example.Main</mainClass>
    </configuration>
</plugin>
```

* iremos alterar o _mainClass_ para colocar o nome do pacote e o nome da classe para compilar
    * Ex: se o nome do pacote é **Template** e o nome da Classe Main é **App** então o mainClass fica assim:
    * `<mainClass>Template.App</mainClass>`


* Instale as modificações: `mvn clean install`

* Compile o Projeto: `mvn compile`

* Para Executar a Classe Main: `mvn exec:java`

### Github Actions Workflow

Agora vamos construir como fazer a compilação automática do projeto Como um Github Action, onde terá as seguintes informações

1. **Determinando a Branch que vai ser testada**

Definimos quando e qual Branch vai rodar a Action, junto com o nome da Action

```yml
name: Java CI With Maven

# Vai rodar no push e pull request na branch main

on:
    push:
        branches: [ main ]
    pull_request:
        branches: [ main ]
```

2. **Criando um Job**

Jobs são usados para criar um conjunto de comandos para serem realizado no teste automatizado

Vamos definir que o Job aconteça dentro do ubuntu

```yml
jobs:
    build:
        runs-on: ubuntu-latest
```

3. **Definindo qual diretório usado no job**

Podemos definir o Diretório default para rodar dentro do job, como abaixo

```yml
jobs:
    build:
        # config do ubuntu(passo 2)

        # o Diretório dentro do Repositório usado será recursividade
        defaults:
            run:
                working-directory: recursividade
```

4. **Definindo os passos feitos no job**

agora vamos usar os `steps` para definirmos a ordem dos passos que vão ser acontecidos dentro do nosso job, onde teremos 3 passos:

```yml
jobs:
    build:
        # config do ubuntu(passo 2)

        # config do diretório principal(passo 3)

        steps:
        # Sistema do actions para fazer checkout
        - uses: actions/checkout@v2

        # PRIMEIRO PASSO: definir e fazer setup do Java
        - name: Configuração do Java 1.8
          uses: actions/setup-java@v1
          with:
            java-version: 1.8

        # SEGUNDO PASSO: Compilar o projeto maven dentro do diretorio recursividade
        - name: Compilando o Projeto
          run: mvn compile

        # TERCEIRO PASSO: Rodar a configuração do pom.xml definido anteriormente
        - name: Rodar o projeto entregando resultado
          run: mvn exec:java
```

* OBS: as tags usadas no Actions
    * `name` = é o nome do passo que vai aparecer no actions
    * `uses` = é a utilização de uma configuração pronta do actions
    * `with` = é definido uma configuração para o actions já pronto, no caso a versão do java
    * `run` = vai ser rodado um comando de Linux, como no terminal do ubuntu, já que estamos usando ele nesse actions