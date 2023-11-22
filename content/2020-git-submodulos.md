+++
title = "Trabalhando com Submódulos no Git"
date = 2020-05-12
description = "Aprenda a adicionar, atualizar e remover submódulos no Git, comandos úteis e referência."

[taxonomies]
tags = ["git"]
+++

Fiz o [ZOLA.386](https://github.com/lopes/zola.386) para ser o tema deste site e a ideia era de trabalhar com detalhes de layout lá, ficando aqui só com conteúdo.  Seguindo indicações da comunidade, implementei isso como um submódulo do repositório do site, mas quando atualizei o ZOLA.386, não vi as mudanças aqui.

Depois de penar um pouco e cometer uns erros, defini algumas ações que precisavam estar à mão para trabalhar com módulos, que detalho a seguir.

## Adicionando um Submódulo
A primeira questão é a adição de um submódulo, que pode ser feita com o comando abaixo, considerando que se esteja dentro do diretório do repositório, no subdiretório onde o módulo ficará:

```bash
$ git submodule add -b master https://github.com/lopes/zola.386
```

O comando informa que se está adicionando um novo módulo (repositório), considerando a *branch* `master`, no caminho (URL) especificado.  Isso criará um diretório no local com o conteúdo do módulo apontado (repositório clonado) e um arquivo `.gitmodules` na raiz do repositório principal.

## Atualizando um Submódulo
Agora a dúvida motivadora deste texto: Quando o submódulo adicionado for atualizado, novas *builds* do repositório principal serão atualizadas?  Não.

O link é feito na versão corrente do repositório apontado e sempre apontará para ele.  Para atualizar, deve-se rodar o seguinte comando dentro do submódulo --`subdir/submodule`:

```bash
$ git pull origin master
```

Como o submódulo também é um repositório Git, rodar esse comando forçará o git a atualizar o submódulo (repositório).  Isso irá requerer um novo *commit* no repositório principal para gravar a atualização.  Com isso é possível ter o repositório principal e o submódulo em versões diferentes.

A partir do Git 1.8 já [é possível executar o comando abaixo](https://stackoverflow.com/questions/8191299/update-a-submodule-to-the-latest-commit), de dentro da raiz do repositório principal, que é mais conveniente, mas ainda exige um novo *commit* para atualizar o ponteiro SHA-1 de versão.

```bash
$ git submodule update --remote --merge
```

## Remoção do Submódulo
Para remover o módulo, é só executar o comando abaixo de dentro do repositório principal, lembrando de substituir `zola.386` pelo nome do módulo:

```bash
$ git submodule deinit zola.386
```

Todos esses comandos, principalmente o primeiro e o último, servem para manipular o arquivo `.gitmodules`.  Apesar desse ser um arquivo texto, evite cair na tentação de alterá-lo manualmente, pois o Git poderá perder a consistência com os metadados do repositório.

Vamo pra próxima!

## Referência
- [Lars Vogel](https://www.vogella.com/tutorials/GitSubmodules/article.html): Esse site tem esses e alguns outros truques para trabalhar com submódulos no Git.
