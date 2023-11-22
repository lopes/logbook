+++
title = "Criando um Tema para o Zola"
date  = 2020-05-11
description = "Aprenda a criar um tema para Zola e contribua com a comunidade."

[taxonomies]
tags = ["zola"]
+++

O [Zola](https://www.getzola.org/) tem um subsistema simples e robusto para criação de templates, o  [Tera](https://tera.netlify.app/), que é baseado no [Jinja2](https://palletsprojects.com/p/jinja/) e no [Django](https://www.djangoproject.com/).  Apesar disso, provavelmente por não ser um projeto muito difundido [ainda], o Zola não tem muitos temas disponíveis, o que obriga o administrador a colocar a mão na massa.

Apesar ser um ponto negativo do Zola, criar um tema é bom pois muitas vezes é preciso mudar algo na estrutura do site.  Além disso, essa é a melhor forma de conhecer um pouco do motor do Zola e contribuir para o ecossistema do projeto.


## História
Eu comecei no Zola usando o [after-dark](https://github.com/getzola/after-dark), mas com pouco tempo de uso já estava insatisfeito, pois ele não era muito amigável para leitura.  Pesquisando por um novo tema, tive a ideia de fazer algo minimalista e retrô e já estava quase começando um CSS do zero, quando esbarrei no [BOOTSTRA.386](https://kristopolous.github.io/BOOTSTRA.386/) para o [Bootstrap](https://getbootstrap.com/) e era exatamente o que queria.

O tema me remete às tardes de sábado, quando eu acompanhava meu pai e um amigo dele lá em casa, instalando e configurando jogos de [MS-DOS](https://en.wikipedia.org/wiki/MS-DOS), no fim dos anos 90.  Só quem viveu essa época sabe o que era esperar dias para baixar um arquivo de poucos MBs e penar em telas de configuração, editando arquivos texto e configurando interrupções.  Resumindo a história, eu tinha achado o modelo que queria e estava pronto para começar.  Passei &approx;4 dias brincando de webdesigner e nos próximos parágrafos resumo o que aprendi e os resultados.


## O Processo
A primeira coisa a se fazer para criar um tema é ter modelos de *design* e de estrutura para seguir, pois o primeiro ajudará no layout final e o segundo, a colocar esse layout no Zola.  No meu caso, usei o BOOTSTRA.386 e o [Dinkleberg](https://github.com/rust-br/dinkleberg), recorrendo ao after-dark e ao [HUGO.386](https://themes.gohugo.io/hugo.386/) para resolver alguns detalhes.  Depois convém definir as integrações web e comigo, identifiquei o [Google Analytics](https://analytics.google.com), o [Disqus](https://disqus.com) e o [Twitter](http://twitter.com).

O próximo passo é a modelagem, que deve ser feita em blocos, visando entender onde há repetições.  Assim, identifiquei a barra de navegação, os títulos de páginas, a barra lateral, o conteúdo da página e o rodapé como blocos principais do site, que se tornariam blocos no Tera para evitar repetição de código.  Comecei modelando pela parte que tende a ser geral em todas as páginas, o cabeçalho HTTP, ajustando variáveis e criando novas para me ajudar no futuro --como a seção para o Google Analytics.

Fui então para a etapa de criação do esqueleto do site em HTML, criando as partes complementares do `index.html`, o `page.html`, o `404.html` e o `macros.html` --esse último é opcional e serve para colocar códigos que se repetem no site.  Com o esqueleto pronto, era hora de aplicar o CSS e os JavaScripts, colocando as marcações de tags de acordo.  Essa etapa demorou, pois envolvia analisar cada elemento do BOOTSTRA.386 e determinar qual o mais adequado para cada seção do site.


## ZOLA.386
Depois de muitos testes, o [ZOLA.386](https://github.com/lopes/zola.386) estava funcionando perfeitamente nos meus testes.  Hora de polir o código, inserir comentários e documentar --ainda não tinha criado um repositório, coisa que fiz na sequência.  Como eu queria contribuir para o Zola, coloquei o código no [formato exigido](https://www.getzola.org/documentation/themes/creating-a-theme/) e pronto.

*Push* para o [GitHub](https://github.com/lopes/zola.386), novo site no [Netlify](https://zola386.netlify.app/), integra, *deploy*, corrige erros, *commit*, repete até ficar certo.  Faltava agora só aplicar o ZOLA.386 no meu site.  Feito isso, e corrigindo mais uns erros, o tema já estava valendo aqui e o [*pull-request* já tinha sido aceito](https://github.com/getzola/themes/pull/26) no repositório oficial de [temas do Zola](https://github.com/getzola/themes).

Essa é a mais pura essência do software livre: Adaptar o software ao seu uso e contribuir com a comunidade.  O ZOLA.386 ficou do jeito que eu queria e estou bastante satisfeito --apesar de saber que vou mexer bastante nele.  Agora dá pra continuar a escrever.

Seguimos.
