# Uma imagem customizada para rodar multiplos serviços em um único conteiner Docker

Primeiramente esta idéia surgiu da necessidade de hospedar mais de um cliente no mesmo servidor na nuvem, a fim de reduzir custos e aproveitar melhor os recursos. Ocorre que até então este processo era realizado diretamente no linux, criando várias pastas uma para cada cliente, e os serviços eram duplicados em várias instancias uma para cada cliente com uma porta de acesso diferente, o que tornava complexo o gerênciamento, além de problemas que as vezes necessitava reiniciar o servidor, o que obrigava derrubar a todos os clientes.

Estava fazendo um curso de Java e neste curso específico havia um tópico falando de Docker, eu já havia ouvido falar mas sempre torci o nariz para esta técnologia, pensando sempre assim "pra mim isto é coisa de nutella que não consegue subir um serviço diretamente no SO...", pois bem mudei completamente meu pensamento.

Durante este curso tive uma idéia "e se em vez de separar os clientes lá por pastas criar um conteiner a cada cliente?!?", bom aqui iniciaram os problemas, meu sistema tem o gerênciador de banco de dados, um serviço principal e mais 6 microserviços, ocorre que eles são fortemente conectados e são exclusivos a cada cliente, então aqui começa inviabilizar o padrão usado no Docker que é um conteiner para um serviço 1:1. Fui na documentação do docker e lá tem um tópico falando que é possivel ter multiplos serviços, porem não há exemplos, além disto pesquisando na web também não se encontra exemplos, no geral o que se encontra são situações que não funcionam na pratica.

Então durante esta pesquisa exaustiva encontrei este repositório [phusion/baseimage-docker](https://github.com/phusion/baseimage-docker) onde foi disponibilizado uma imagem mínima de Ubuntu com alguns serviços instalados syslog, ssh e cron. Peguei o exemplo e dei uma estudada, basicamente ele criou um serviço chamado **my_init** que em cima do *runit* (que é um gerenciador de serviços do linux bem enxuto), no geral as imagens não tem controle de serviços, mesmo que instale um ubuntu limpo, você não terá o systemd ou o init.d mesmo que as pastas existam, então este cara resolve isto, pois ao iniciar o conteiner é rodado este serviço chamado **my_init** onde tudo que estiver acoplado a ele é iniciado ou parado quando necessário.

# O que esta imagem tem de diferente da original?

Antes de mais nada gostaria de agradecer e fazer menção ao **@phusion** e sua equipe que disponibilizaram este fonte de forma gratuíta e livre.

Eu não fiz um *fork* do orginal pois mudei praticamente toda estrutura deixando somente biblioteca do **my_init** e o serviço do **syslog-ng** pois log em um ambiente de multiplos serviços é importante, o restante foi removido da imagem base, para ficar mais enxuta e facil de entender por outras pessoas.

Também coloquei um pack de ferramentas adicionais que estou mais ambientado a utilizar.

Então vamos ao que interessa sobre o que temos nesta imagem:

* Base: ubuntu:latest, oficial.
* Ferramentas essencias: runit (gerenciador de serviços), psmisc (para controle de kill e arvor de processos)
* Ferramentas Extras: curl (permite donwloads), tar (descompactação), nano (editor de texto), apt_install_clean (script personalizado para rodar o apt de forma minima e limpa)
