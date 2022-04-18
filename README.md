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

## Fonte
https://willschenk.com/articles/2018/building-a-hugo-site/
