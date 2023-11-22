+++
title = "Aprendendo a Criar Sites Estáticos com Zola"
date  = 2020-04-27
description = "Descubra como o Zola, um construtor de sites estáticos escrito em Rust, pode ser uma ótima opção para criar e manter sites seguros e de alta performance."

[taxonomies]
tags = ["blogging", "git", "zola"]
+++

Por já ter trabalhado com gerenciadores de conteúdo como [Drupal](https://www.drupal.org/), [Wordpress](https://wordpress.org/) e até [PHP Nuke](https://en.wikipedia.org/wiki/PHP-Nuke) --esse entrega a idade--, sei como é complicado manter atualizações de segurança e lidar com sistemas PHP complexos, cheios de plugins com códigos desconhecidos, que põem em risco a segurança e o desempenho de qualquer portal.

Isso me fez dar muito valor a sites estáticos, tanto que eu consegui convencer um cliente, usando dados do Google Analytics, a migrar de um portal movido a Wordpress para um site estático feito com [Bootstrap](https://getbootstrap.com/).  Na época, por volta de 2015, eu fiz o site manualmente, mas lembro de me perguntar frequentemente como seria se fosse um site maior.


## Sites Estáticos
Pouco tempo depois, tive contato com o [Hugo](https://gohugo.io/), um gerador de sites estáticos muito interessante, mas não tive oportunidade de colocá-lo em produção.  Em 2018, no meu trabalho principal, iniciei junto com meu time a criação de um portal de documentação e cheguei a especular o uso do Hugo, mas acabei optando pelo [Sphinx](https://www.sphinx-doc.org/), principalmente por causa do tema [Read the Docs](https://sphinx-rtd-theme.readthedocs.io/) --ainda vou comentar em detalhes como foi esse projeto.

> Quem me conhece, sabe o quanto eu gosto e incentivo o uso de [Python](https://www.python.org/), que inclusive foi um dos motivos por eu ter optado pelo Sphinx.  Só que alguns meses atrás eu tomei conhecimento da linguagem [Rust](https://www.rust-lang.org/) e a coloquei no meu radar para aprender, principalmente por ser compilada e ter um forte apelo de segurança.


## Zola
Quando eu resolvi criar este portal, no início de 2020, optei por usar o [Zola](https://www.getzola.org/), principalmente por ser escrito em Rust e prometer ser muito rápido.  O Zola é um construtor de sites muito simples --possui apenas um executável--, mas também bastante poderoso.  Pelas minhas pesquisas, ele é comparável ao famoso Hugo e vem até com um servidor para poder visualizar o site enquanto está sendo desenvolvido.

A curva de aprendizagem é mínima, principalmente por usar markdown, o que também facilita no versionamento, que pode ser feito com Git.  Como o [Bruno Rocha fez um tutorial](https://codeshow.com.br/criando-seu-blog-com-zola-e-hospedando-de-graca-na-netlify/) muito bom sobre o assunto, acho que não preciso repetir aqui, mas o código-fonte deste site está publicado no meu perfil do [GitHub](https://github.com/lopes/anchor), então pra ficar até mais fácil é só *forkar* lá e personalizar.

> Como os temas são adicionados como submódulos Git, é necessário passar a *flag* `recursive` na clonagem:

```sh
$ git clone --recursive https://github.com/lopes/anchor
```

Outra vantagem dessa abordagem é a publicação, que pode fazer uso de ferramentas de deploy automático, facilitando bastante a vida.  No meu caso, eu clono o repositório do GitHub, edito tudo no meu editor de textos, vejo como está ficando usando o próprio Zola, salvo, *commito* e quando faço o *push*, a nova versão é empurrada para a produção automagicamente.

Vamos em frente!
