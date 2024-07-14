{{ $file := .Get 0 }}
{{ with resources.GetRemote $file }}
    {{ with .Err }}
        {{ errorf "%s" . }}
    {{ else }}
        {{ .Content | $.Page.RenderString }}
    {{ end }}
{{ else }}
    {{ errorf "Unable to get remote resource." }}
{{ end }}
