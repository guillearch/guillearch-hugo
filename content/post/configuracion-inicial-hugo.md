---
title: "Primeros pasos tras instalar Hugo"
date: 2018-09-06T06:54:02+02:00
draft: false
author: Guillermo Castellano
---

En esta entrada te cuento los ficheros que yo he modificado tras instalar Hugo y el tema [Hello Friend](https://github.com/panr/hugo-theme-hello-friend) para adaptar el sitio a mis necesidades.

## Prerrequisitos

* Hemos instalado Hugo.
* Hemos instalado el tema de nuestra elección.
* Estamos familiarizados con los comandos básicos de Hugo: `hugo server`, `hugo new post/tu-post.md`, etc.

## Objetivo

* Modificar el permalink de los posts.
* Añadir el enlace al feed de RSS.
* Configurar la forma en que el sitio aparece en los lectores de RSS.
* Cambiar el idioma de algunos elementos del tema.
* Añadir un favicon al sitio.

## Permalink de los posts

Por defecto, los posts de un nuevo sitio hecho con Hugo se publican con el permalink `tu-sitio/post/tu-post`.

Yo quería cambiar la URL al formato `tu-sitio/tu-post`, que aparentemente es el que mejor funciona para [SEO](https://yoast.com/wordpress-seo-url-permalink/).

Para ello, lo único que tuve que hacer fue añadir las dos siguientes líneas a mi archivo **config.toml**:

```
[permalinks]
    post = "/:title/"
```

Para asegurarte de que se ha cambiado correctamente, puedes crear un nuevo post de prueba y ver la URL que devuelve ese post en el navegador.

Sobra decir que puedes indicar otras estructuras, como la clásica `tu-sitio/año/mes/día/tu-post`.

La estructura `tu-sitio/categoría/tu-post` es más compleja de resolver, ya que las taxonomías en Hugo no funcionan igual que en otros gestores de contenido. Te dejo una discusión sobre posibles soluciones en el [foro de Hugo](https://discourse.gohugo.io/t/does-hugo-support-categories-in-the-permalink/984/13).

## Enlace al feed de RSS

Hugo ofrece soporte nativo para RSS, así que no tienes que configurar nada para que puedan empezar a seguir tu blog utilizando un lector de RSS.

Es posible que te pase como a mí y quieras agregar en tu menú de navegación un enlace a tu feed de RSS para que tus lectores lo encuentren fácilmente. Todo lo que tienes que hacer es añadir estas cuatro líneas en la sección `[menu]` del fichero **config.toml**:

```
[[menu.main]]
  identifier = "rss"
  name = "RSS"
  url = "/post/index.xml"
```

## Modificación de la plantilla de RSS

Mi siguiente paso fue crear en el directorio `themes/mi-tema/_default/` un fichero **rss.xml** con el siguiente contenido:

```
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ if eq  .Title  .Site.Title }}{{ .Site.Title }}{{ else }}{{ with .Title }}{{.}} on {{ end }}{{ .Site.Title }}{{ end }}</title>
    <link>{{ .Permalink }}</link>
    <description>Recent content {{ if ne  .Title  .Site.Title }}{{ with .Title }}in {{.}} {{ end }}{{ end }}on {{ .Site.Title }}</description>
    <image>
      <url>{{ "img/tu-imagen.png" | absURL }}</url>
      <title>{{ if eq  .Title  .Site.Title }}{{ .Site.Title }}{{ else }}{{ with .Title }}{{.}} on {{ end }}{{ .Site.Title }}{{ end }}</title>
      <link>{{ .Permalink }}</link>
    </image>
    <generator>Hugo -- gohugo.io</generator>{{ with .Site.LanguageCode }}
    <language>{{.}}</language>{{end}}{{ with .Site.Author.email }}
    <managingEditor>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</managingEditor>{{end}}{{ with .Site.Author.email }}
    <webMaster>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</webMaster>{{end}}{{ with .Site.Copyright }}
    <copyright>{{.}}</copyright>{{end}}{{ if not .Date.IsZero }}
    <lastBuildDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</lastBuildDate>{{ end }}
    {{ with .OutputFormats.Get "RSS" }}
        {{ printf "<atom:link href=%q rel=\"self\" type=%q />" .Permalink .MediaType | safeHTML }}
    {{ end }}
    {{ range .Pages }}
    <item>
      <title>{{ .Title }}</title>
      <link>{{ .Permalink }}</link>
      <pubDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</pubDate>
      {{ with .Site.Author.email }}<author>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</author>{{end}}
      <guid>{{ .Permalink }}</guid>
      <description>{{ .Content | html }}</description>
    </item>
    {{ end }}
  </channel>
</rss>
```

La etiqueta `<image>...</image>` sirve para agregar al feed la imagen alojada en `img/tu-imagen.png`.

Por su parte, la línea `<description>{{ .Content | html }}</description>` hace que en el lector de RSS aparezca todo el contenido del post en lugar de un resumen.

Hugo sigue la [especificación RSS 2.0](https://cyber.harvard.edu/rss/rss.html).

## Traducir algunos elementos del tema

Salvo que tengas un sitio multilingüe, es posible que te interese cambiar el idioma en el que vienen por defecto algunos elementos de tu tema, como "Written by" o "Read more" y los nombres de las secciones.

Lo primero lo hice modificando los ficheros HTML del directorio `hemes/mi-tema-layouts/_defaults`.

Los nombres de la sección se pueden modificar cambiando el valor del atributo `name` en el fichero **config.toml**:

```
[menu]
  [[menu.main]]
    identifier = "about"
    name = "Acerca de mí"
    url = "/about"
  [[menu.main]]
    identifier = "contact"
    name = "Contacto"
    url = "/contact"
```

## Añadir un favicon

Para añadir un favicon a tu sitio (la imagen que aparece junto al nombre de tu página en el navegador), solo tienes que crear el archivo `static/favicon.ico` y añadir esta línea en el archivo **head.html** (en caso de que no exista ya):

```
<link rel="shortcut icon" href="/favicon.ico?">
```

Todo el contenido alojado en el directorio `static/` se sirve en la raíz de tu sitio. Así, el archivo `img/favicon.ico` aparecerá en la URL `tu-sitio/favicon.ico`. Es por ello que en la línea anterior indicamos el enlace `/favicon.ico` en lugar de `img/favicon.ico`.

Eso es todo.

¡Espero que te resulte útil!
