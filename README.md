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

```



## Fonte
https://willschenk.com/articles/2018/building-a-hugo-site/
