# Construindo um site e tema hugo com Bootstrap

Primeiro passo é baixar o hugo do site principal . E então vamos criar um site e um tema em branco.
```
$ hugo new site willschenk-hugo-bootstrap
$ cd willschenk-hugo-bootstrap
$ hugo new theme mytheme
```

Edite o arquivo gerado `config.toml` para que ele aponte para este novo tema.
```
baseURL = 'https://suzano.github.io/willschenk-hugo-bootstrap/'
languageCode = 'en-us'
title = 'My New Hugo Site'
theme = 'mytheme'
relativeURLs = true
```

Eu gosto de configurar todos os sites de conteúdo para que as urls sejam relativas para que eu possa hospedar em subdiretórios e afins. Finalmente, vamos iniciar o servidor:
```
$ hugo server --watch --verbose --buildDrafts --cleanDestinationDir
```

## Layouts, blocos e parciais

Primeiro vamos começar com o layout base em `themes/mytheme/layouts/_default/baseof.html`:
```
<!DOCTYPE html>
<html>
  {{- partial "head.html" . -}}
  <body>
    {{- partial "header.html" . -}}
    {{- block "main" . }}{{- end }}
    {{- partial "footer.html" . -}}
  </body>
</html>
```

Este é o layout base `_default` para tudo no site. Você pode ter layouts específicos para diferentes tipos de conteúdo, mas este é o fallback que vamos definir para quando nada mais for especificado. Dentro disso, definimos alguns `partials` e `blocks`. Você pode pensar em parciais como submodelos incluídos que podemos usar tanto para estrutura quanto como um local conveniente para substituir a estrutura de um tema quando você deseja fazer grandes personalizações e blocks são substituídos pelos dados do conteúdo do seu site, portanto, são mais bem pensados ​​como as partes em que o modelo é preenchido com dados.

O material `{{ }}` é a linguagem de templates go, então se você quiser realmente descobrir como isso funciona, você acabará se encontrando nos documentos da linguagem golang, que às vezes é um pouco intenso. A principal coisa a lembrar é que para cada página que é renderizada no site é passada em um objeto `HugoPage` que é onde você precisa extrair todos os dados. Então, se você quer saber o que está disponível para você, é aí que você começa a procurar.

## Cabeça

Dentro `themes/mytheme/layouts/partials/head.html`, adicione bootstrap e os vários pedaços de grandiosidade necessários para isso.
```
<head>
  <title>{{ .Title }}</title>
  <!-- Required meta tags -->
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
  <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
  <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/js/bootstrap.min.js" integrity="sha384-ChfqqxuZUCnJSK3+MXmPNIyE6ZbWh2IMqE241rYiqJxyMiZ6OW/JmZQ5stwEULTy" crossorigin="anonymous"></script>

  {{ range .AlternativeOutputFormats -}}
    {{ printf `<link rel="%s" type="%s+%s" href="%s" title="%s" />` .Rel .MediaType.Type .MediaType.Suffix .Permalink $.Site.Title | safeHTML }}
  {{ end -}}

</head>
```

Isso configura o bootstrap para carregar do CDN, além de adicionar um link ao feed rss gerado. Aqui podemos ver algumas outras coisas. Uma é que estamos configurando o `<title>using {{ .Title }}`. Podemos querer mudar isso mais tarde, mas o que isso significa é que estamos usando o atributo title da página atual para definir o título e, se você quiser incluir algo dos dados `.Site`, é aqui que você deve colocar a lógica.

A outra coisa aqui é a `range` função. Em termos práticos, estamos colocando `rss` links de descoberta, mas o que isso literalmente está fazendo é fazer um loop sobre a `AlternativeOutputFormats` fatia dentro da página `range` é o idioma de loop golang, que é como um `for` loop, mas melhor.

## Rodapés
Vamos colocar algumas dessas variáveis ​​no rodapé para ajudar a depurar um pouco. É `themes/mytheme/layouts/partials/footer.html` aqui que vamos expor as variáveis ​​da página hugo para sabermos o que está acontecendo
```
<footer class="footer mt-5">
  <div class="container">
    <table class="table table-sm">
      <caption>Hugo Variables for current page</caption>
      <tr><th>Name</th><td>{{ .Name }}</td></tr>
      <tr><th>Kind</th><td>{{ .Kind }}</td></tr>
      <tr><th>Type</th><td>{{ .Type }}</td></tr>
      <tr><th>List Page</th><td>.Pages</td></tr>
      <tr><th>IsPage</th><td>{{ .IsPage }}</td></tr>
      <tr><th>IsHome</th><td>{{ .IsPage }}</td></tr>
      <tr><th>Next</th><td>{{ .Next }}</td></tr>
      <tr><th>Prev</th><td>{{ .Prev }}</td></tr>
      <tr><th>Section</th><td>{{ .CurrentSection }}</td></tr>
    </table>
  </div>
</footer>
```

## Modelos de página

Agora vamos definir um único template básico, que será usado para renderizar um único objeto, não uma coleção de nada. Vamos demonstrar isso inicialmente com a página de índice `themes/mytheme/layouts/_default/single.html`:
```
{{ define "main" }}
<div class="container">
  <p>This from the single page template</p>
</div>
{{ end }}
```

E saiu o primeiro rascunho do template `index.html` para o site. Vamos colocar um pequeno jumbotron em `themes/mytheme/layouts/index.html`:
```
{{ define "main" }}
<div class="jumbotron">
  <div class="container">
    <h1 class="display-4">This could be the title</h1>
    <p class="lead">Here's some description stuff</p>
    <p>There's also other things that are super nice</p>
  </div>
</div>
{{ end }}
```

Neste ponto, você deve ser capaz de ver a página de índice quando for para http://localhost:1313 (Se hugo não criou um header.html parcial vazio, a compilação pode falhar, então vá para a próxima seção se você não não tem isso.)

## Cabeçalho e menus

No `config.toml` nível superior do seu site (fora do tema) vamos definir alguns itens de menu. Em seguida, usaremos esses dados para preencher o cabeçalho.
```
[menu]
  [[menu.main]]
    identifier = "about"
    name = "About"
    url = "/about/"

  [[menu.main]]
    identifier = "posts"
    name = "Posts"
    url = "/posts/"
```

Em seguida, adicione um cabeçalho em `themes/mytheme/layouts/partials/header.html`. Vamos usar uma barra de navegação bootstrap e retirar os itens do menu da configuração do site.
```
<nav class="navbar navbar-expand-lg navbar-light ">
  <a class="navbar-brand" href="{{ "/" | relURL}}">{{.Site.Title}}</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav ml-auto">
      {{ $currentPage := . }}
      {{ range .Site.Menus.main }}
        <li class="navbar-item {{if or ($currentPage.IsMenuCurrent "main" .) ($currentPage.HasMenuCurrent "main" .) }} active{{end}}">
          <a class="nav-link" href="{{ .URL }}" title="{{ .Title }}">{{ .Name }}</a>
        </li>
      {{ end }}
    </ul>
  </div>
</nav>
```

Há alguns conceitos novos aqui, o primeiro é `{{ "/" | relURL }}` que é uma função auxiliar para transformar uma variável em algo que possa torná-la mais palatável para o usuário. Neste caso, estou apenas traduzindo `/` para um `relativeURL`, que em subpáginas terá algo como `../../index.html` ou qualquer outra coisa.

`{{ $currentPage := . }}` está definindo uma variável, então vamos nos referir ao contexto externo quando estivermos `range` abaixo. Os `range` links sobre todos os itens do menu. O objeto que `.` se refere às alterações dentro do `range` para ser o item atual sobre o qual ele está iterando, portanto, para fazer comparações com o `currentPage` nesse caso, precisamos dar um nome a ele `:=` é muito golang.

Também são declarações `if`, que estamos usando para determinar se incluímos a classe `active` no link. Observe onde está o operador, ou seja, `or` vem primeiro e os dados vêm depois. Tão límpido!

## Adicionando conteúdo de postagem

Vamos agora adicionar alguns posts básicos.
```
$ hugo new posts/sample_post.md
```

Se você não iniciou o hugo com -D, você precisará ter certeza de que o sinalizador de rascunho não é verdadeiro para que ele apareça.
```
---
title: "Sample Post"
date: 2018-10-19T16:04:51-04:00
draft: false
---

One of the things I'm very interested in is writing words and seeing them on the page.
```

Isso é bastante padrão na maioria dos geradores de sites estáticos com a matéria da frente na parte superior e a remarcação na parte inferior.

## Modelos de lista

Crie uma visualização de modelo de lista em `themes/mytheme/layouts/_default/list.html`:
```
{{ define "main" }}
<div class="container">
  <h1 class="text-center">
    {{ .Type | humanize }}
  </h1>
  {{ range .Pages }}
    <div class="row mt-2">
      <div class="d-none d-sm-block col-sm-2 mt-auto offset-sm-2 text-right">
        <time class="post-date" datetime="{{ .Date.Format "2006-01-02T15:04:05Z07:00" | safeHTML }}">{{ .Date.Format "Jan 2, 2006" }}</time>
      </div>
      <div class="col">
        <a class="text-body" href="{{ .URL | relURL }}">{{ if .Draft }}DRAFT: {{end}}{{ .Title | markdownify }}</a>
      </div>
    </div>
  {{ end }}
</div>
{{ end }}
```

Estamos usando uma função hugo humanize para capitalizar o tipo de objeto que estamos olhando e um monte de classes de utilitário bootstrap para alinhar e mostrar apenas a data publicada em telas maiores.

A string `.Date.Format` de formato é realmente estranha – não tenho certeza se realmente a entendi, mas espera-se que o ano seja 2006 para que as coisas façam sentido. Isso é parte da maneira como o material de formatação de data de go subjacente do hugo funciona.

Agora clicamos em postagens na barra de navegação, você deve ver a lista de postagens. Mas quando clicamos na própria página, você notará que temos o texto “This from the single page template”. Então vamos atualizar esse `themes/mytheme/layouts/_default/single.html` modelo agora:
```
{{ define "main" }}
<div class="container">
  <h1>{{ .Title | markdownify}}</h1>

  {{ .Content }}
</div>
{{ end }}
```

## Adicionando tags

Já que mencionamos as tags acima, vamos em frente e adicionar essa taxonomia ao nosso tema. Outro tipo comum de taxonomia são as categorias. Vamos em frente e adicionar isso ao `theme.toml`:
```
tags = ["tags"]
```

Reinicie seu servidor hugo para ver a mágica!

Vamos adicionar algumas tags ao nosso primeiro arquivo `sample_post.md`:
```
---
title: "Sample Post"
date: 2018-10-19T16:10:36-04:00
draft: false
tags: [ "one", "two" ]
---

This is the first paragraph of what I'd like to say.
```

E depois crie outro post
```
$ hugo new posts/sample_post_the_second.md
```

E preencha com:
```
---
title: "Sample Post The Second"
date: 2018-10-19T16:54:29-04:00
draft: false
tags: ["two"]
---

This is the second amazing post that will *blow your mind*!
```

Novo permite adicionar tags ao nosso menu para que possamos colocá-lo na barra de navegação. Também especificaremos os pesos do menu para corrigir o pedido. Isso é feito na configuração do site `config.toml` onde estamos configurando como este site específico usou o tema que estamos definindo.
```
[menu]
  [[menu.main]]
    identifier = "about"
    name = "About"
    url = "/about/"
    weight = 100

  [[menu.main]]
    identifier = "tags"
    name = "Tags"
    url = "/tags/"
    weight = 110

  [[menu.main]]
    identifier = "posts"
    name = "Posts"
    url = "/posts/"
    weight = 120
```
Finalmente, vamos adicionar alguma lógica ao nosso tema `theme/mytheme/partials/header.html`. Você pode substituir a coisa toda.
```
<nav class="navbar navbar-expand-lg navbar-light ">
  <a class="navbar-brand" href="{{ "/" | relURL}}">{{.Site.Title}}</a>
  <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
    <span class="navbar-toggler-icon"></span>
  </button>

  <div class="collapse navbar-collapse" id="navbarSupportedContent">
    <ul class="navbar-nav ml-auto">
      {{ $currentPage := . }}
      {{ range .Site.Menus.main }}
        {{ if not (eq .Identifier "tags") }}
          <li class="navbar-item {{if or ($currentPage.IsMenuCurrent "main" .) ($currentPage.HasMenuCurrent "main" .) }} active{{end}}">
            <a class="nav-link" href="{{ .URL | relURL }}" title="{{ .Title }}">{{ .Name }}</a>
          </li>
        {{ else }}
          <li class="nav-item dropdown">
            <a class="nav-link dropdown-toggle" href="{{ .URL | relURL }}" id="navbarDropdown" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
              {{ .Name }}
            </a>
            <div class="dropdown-menu" aria-labelledby="navbarDropdown">
              {{ range $name, $taxonomy := $currentPage.Site.Taxonomies.tags }}
                {{ with $.Site.GetPage (printf "/tags/%s" $name) }}
                  <a class="dropdown-item" href="{{ .URL | relURL }}">{{ $name }}</a>
                {{ end }}
              {{ end }}
            </div>
          </li>
        {{ end }}
      {{ end }}
    </ul>
  </div>
</nav>
```

ERRO - <a class="dropdown-item" href="{{ relURL }}">{{ $name }}</a> no arquivo header.html

## Fonte
https://willschenk.com/articles/2018/building-a-hugo-site/
