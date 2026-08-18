# ex288

https://github.com/redhat-developer-demos/react-openshift-example
https://github.com/tech-tejendra/EX288 -b source-build -folder nodejs-helloworld
https://www.youtube.com/playlist?list=PLnFCwVWiQz4lrK7s1S409fsfm59qdFVOX - TEJENDRA
https://www.youtube.com/playlist?list=PLpl3SsvEUof6l9ATbOT-9wi5FqLMIxVAP - OMAR

Repo:
https://github.com/rlaskew/openshift-ex288-training
https://github.com/lutfigaranti/DO288/tree/master/s2i-do288-httpd
https://github.com/jeromeza/EX288-PREP
https://github.com/tsrana

cd C:\_dev\ex288\ex288
git add . && git commit -m "fix" && git push

=================================================================================================================================================
=================================================================================================================================================

É possível abrir um notepad ou vscodium
O user do git já está setado não precisa adicionar em lado nenhum, mas existe a informação de user/pass nas informações.

******

Questão 01 - Fazer deploy de uma aplicação baseado no codigo de um repositório git.

npm_config_registry=http://nexus-common.apps.na410.prod.nextcle.com/repository/nodejs

Informações da questão:
- Nome do projeto
- Nome do app
- Endereço do git: git.com/project
- A image para usar
- Variável para colocar no buildconfig relacionado a npm_config_registry ao repo http://nexus-common.apps.na410.prod.nextcle.com/repository/nodejs
- expor o deployment na porta 3000
- Comando curl para teste

Observação: É possível resolver essa questão via interface.
Observação: Existe um erro no package.json, uma vírgula está no lugar errado logo no início do arquivo.
Observação: É possível verificar o erro no package.json usando o comando python3 -m json.tool package.json
Observação: Corrigir o package.json antes de fazer build!!!

oc new project <project_name>
oc new-app --name <nome_APP> <imagem-base>~<repositorio-git> --build-env <npm_var>=<npm_repository>

Verificar logs do build na interface e alterar o arquivo package.json e fazer push para o repositorio git.

mkdir q1
cd q1
git clone <repositorio-git>
cd <project_folder>

vi package.json
git add . && git commit - m "json fix" && git push

oc start-build bc/<nome-do-build> (VALIDAR COMANDO) OBSERVAÇÃO: é possível fazer pela interface

oc expose deploy/<nome_APP> --port 3000
oc expose service/<nome_APP>
curl <url_teste>

Vídeos para ajudar!
https://www.youtube.com/watch?v=_SKDLMbISho
https://www.youtube.com/watch?v=V69I-M1AXs4

=================================================================================================================================================
=================================================================================================================================================

Questão 2 - Fazer deploy de uma aplicação com Dockerfile:

Informações da questão:
- Nome do projeto
- Nome do app
- Endereço do git: git.com/project
- Endereço do binário
- A image para usar
- Comando curl para teste

Observação: O questão informa que não é permitido fazer push no repositorio
Observação: O path que obtém o binário que é usado no Dockerfile está errado tem de ser alterado via ARG
Observação: Verificar qual a porta que é exposta no Dockerfile.

mkdir q2
cd q2
git clone <repositorio-git>
cd <project_folder>

oc new-project <project_name>
oc new-app --name <nome_APP> <imagem-base>~<repositorio-git> --build-env <valor-inidicado-enunciado> --strategy docker (ANALISAR COMANDO)
oc expose deploy/<nome_APP> --port 3000 (ANALISAR COMANDO)
oc expose svc/<nome_APP> (ANALISAR COMANDO)
curl <url_teste>

Vídeos para ajudar!
https://www.youtube.com/watch?v=UUHsSFCce4c
https://www.youtube.com/watch?v=3xjCS5hLkkM

=================================================================================================================================================
=================================================================================================================================================

Questão 3 - Fazer deploy de uma aplicação com custom S2I:

Informações da questão:
- Nome do projeto
- Nome do app
- Endereço do git: git.com/project
- A image para usar
- Comando curl para teste

mkdir q3
cd q3
git clone <repositorio-git>
cd <project_folder>
vi .s2i/bin/run (ls -a para ver a pasta)

Alterar o arquvo run e adicionar export de uma porta: export SERVER_PORT=8081 (EM QUAL MOMENTO DO RUN FAZER ISSO??)

oc new-project <project_name>
oc new-app --name <nome_APP> <image-base>~<repositorio-git> --strategy=source (QUAL STRATEGY???)
oc expose deploy/<nome_APP> --port 8081 (ANALISAR COMANDO)
oc expose svc/<nome_APP> (ANALISAR COMANDO)
curl <url_teste>

(VERIFICAR TEMA)
Dúvida: essa pergunta também falava qualquer coisa de ter uma tag latest, essa parte não percebi muito bem, mas criei uma tag da imagem base e criei a aplicação com ela:
oc tag <image-base>:<tag-base> <imagem-base>:<latest>
oc new-app --name <nome> <imagem-base>~<repositorio-git>

Vídeos para ajudar!
https://www.youtube.com/watch?v=CD_dC9CYOnQ
https://www.youtube.com/watch?v=wTsc73OKbRc

=================================================================================================================================================
=================================================================================================================================================

Questão 4 - Criar ConfigMaps e Secrets e injetar no deployment

Informações da questão:
- Informações para criação do configmap, o secret já está criado
- Fazer expose do podname
- Comando curl para teste

Observação: É possível resolver GRANDE PARTE da questão via interface.

oc create configmap <configmap-name> --from-literal KEY=VALUE --from-literal KEY=VALUE (ANALISAR COMANDO)
oc set env dc/<nome_APP> --from cm/<configmap-name> (ANALISAR COMANDO)
oc set env dc/<nome_APP> --from secret/<secret-name> (ANALISAR COMANDO)
curl <url_teste>

FAZER EXPOSE DO POD_NAME NO DEPLOYMENT: Tentei alterar o configmap e adicionar a seguinte propriedade POD_NAME=metadata.name
mas mesmo após fazer reload no configmap e restart rollout no deployment não apareceu, adicionei manualmente no deployment mas acho que não foi a solução correta.

Vídeos para ajudar!
https://www.youtube.com/watch?v=5ywrz5AMLO4

=================================================================================================================================================
=================================================================================================================================================

Questão 5 - Criar 3 probes liveness readiness startup, onde a aplicação não pode cair após adicionar os probes

Informações da questão:
- Informações para criação do liveness probe
- Informações para criação do readiness probe
- Informações para criação do startup probe
- Comando curl para teste

Observação: É possível resolver essa questão via interface.
Observação: Esperar um tempo para e acompanhar os logs.

oc set probe deploy/<nome_APP> --startup --period-seconds=0 --initial-delay-seconds=10 --failure-threshold=0 --timeout-seconds=30 (ANALISAR COMANDO)
oc set probe deploy/<nome-deploy> --liveness --initial-delay-seconds=10 --failure-threshold=0 --timeout-seconds=30 (ANALISAR COMANDO)
oc set probe deploy/<nome-deploy> --readiness --get-url=http://:8080/health/readiness ==> (DE ONDE VEIO ESSA URL) (ANALISAR COMANDO)
curl <url_teste>

DÚVIDA SOBRE URLS, somente um probe tinha a url http://:8080 os outros não tinham nenhuma url, deixei default

Vídeos para ajudar!
https://www.youtube.com/watch?v=xGWUypNSTd4
https://www.youtube.com/watch?v=11WYKIOKo9E

=================================================================================================================================================
=================================================================================================================================================

Questão 6 - Criar aplicações com base em templates

Informações da questão:
- repositorio onde está o template de build
- repositorio onde está o template de deploy
- inserir parâmetro no template de build
- informações sobre quais campos são necessários substituir no template de build (São muitas as informações)
- informações sobre quais campos são necessários substituir no template de deploy (são poucas coisas a se alterar)
- Comando curl para teste

Observação: Existia uma nota que dizia que o parâmetro a ser inserido no template de build já existia, mas não achei nada sobre isso.
Observação: Já existe uma aplicação funcionando em um projeto, deve-se criar a mesma aplicação com base no template em outro projeto

mkdir q6
cd q6
git clone <repositorio-git>
cd <project_folder>
vi template-build.yaml (Adicionar parâmetro necessário e verificar senão existe nada de estranho)

oc new-project <project_name>
oc apply -f <path_template_build>
oc new-app <nome_template> -p APP_NAME=<nome_app> -p GIT_URL=? -p GIT_REF=? (Precisa colocar aspas????)
verificar logs no topology

(CASO OCORRA ERRO COMO REMOVER O QUE O TEMPLATE CRIOU???)

oc apply -f <path_template_deploy>
oc new-app <nome_template> -p APP_NAME=<nome_app> -p GIT_URL=? -p GIT_REF=? (Precisa colocar aspas????)
curl <url_teste>

DÚVIDAS:
01 - Qual a diferença entre as propriedades GIT_URL e GIT_REF, não consegui fazer o build funcionar sempre dava falha ao tentar fazer clone do git.
02 - Tinha uma parte dizendo que todos as coisas criadas pelo template tinha que ter o selector(app: batatas, coisa: batata), onde colocar essa informação.

Vídeos para ajudar!
https://www.youtube.com/watch?v=DFd0eu7pzZo
https://www.youtube.com/watch?v=VxsfXn6B6m4

=================================================================================================================================================
=================================================================================================================================================

Questão 7 - Fazer deploy de uma aplicação baseada em helm charts

Informações da questão:
- repositorio de um projeto onde existe os helm charts
- atributos que devem ser adicionados tanto no values quanto em devel e production (CRIAR, pois não existe no values???)
- configuração dos recursos como memória e processador
- deve ser possível mudar de ambiente após ser construída
- Comando curl para teste

Observação: Existem dois ambientes production.yaml e devel.yaml ambos os yamls não tem nenhuma informação
Observação: Analisar o values.yaml e o conteudo dos templates (deployments, routes, etc) para perceber que values configurar
Observação: Há mais uma propriedade para o route ter https, é necessário analisar o yaml do route para saber o que alterar
Observação: É necessário adicionar configuração de recurso por exemplo: (VER DOCUMENTAÇÃO DO YAML!!!)
resourses:
limits:
cpu:
memory:

mkdir q7
cd q7
git clone <repositorio-git>
cd <project_folder>
vi values.yaml
vi devel.yaml (Adicionar coisas expecifícas do ambiente)
vi production.yaml (Adicionar coisas expecifícas do ambiente)

alterar o ficheiro devel.yaml com:

image:
repository:

stage: devel

resourses:
limits:
cpu:
memory:

alterar o ficheiro production.yaml com:

image:
repository:

replicaCount:

stage: production

resourses:
limits:
cpu:
memory:

DÚVIDA: criar um projeto para devel e outro para production ou a aplicação deve-se executar algum comando para criar a aplicação com base em outro yaml???
Não lembro de ter visto essa informação!
O nome do helm install é o nome de que?

oc project <nome-do-projeto>
helm install <nome????> --values=values.yaml --values=devel.yaml
oc project <nome-do-projeto>
correr: helm install <nome????> --values=values.yaml --values=production.yaml

Vídeos para ajudar!
https://www.youtube.com/watch?v=KkEm8R_4HcU
https://www.youtube.com/watch?v=mPS3qa7KUVU

=================================================================================================================================================
=================================================================================================================================================

8 - ci/cd pipelines

colocar uma nova propriedade na pipelines
colocar para a task maven-build

- maven-build:
  image: ''

  &(params.)

  oc apply -f

  tkn

Vídeos para ajudar!
https://github.com/OPerezSandoval/ex288-cert/tree/main/question-10-openshift-pipelines-and-triggers