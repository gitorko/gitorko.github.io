{{ $content := readFile (.Get 0) }}
{{ $dir := path.Dir (.Get 0) | strings.TrimPrefix "content" }}

{{ $markdownPattern := `!\[(.*?)\]\((.*?)\)` }}
{{ $htmlPattern := `<img(.*?)src="(.*?)"(.*?)>` }}

{{ $content = replaceRE $markdownPattern (printf "![${1}](%s/${2})" $dir) $content }}
{{ $content = replaceRE $htmlPattern (printf "<img${1}src=\"%s/${2}\"${3}>" $dir) $content }}

{{ $content | markdownify }}
