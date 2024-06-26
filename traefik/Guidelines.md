# Traefik Helm Chart Guidelines

This document outlines the guidelines for developing, managing and extending the Traefik helm chart.

This Helm Chart is documented using field description from comments with [helm-docs](https://github.com/norwoodj/helm-docs).

Optionality
All non-critical features (Features not mandatory to starting Traefik) in the helm chart must be optional. All non-critical features should be disabled (commented out) in the values.yaml file. All optional non-critical features should be disabled (commented out) in the values.yaml file, and have a comment # (Optional) in the line above. This allows minimal configuration, and ease of extension.

## Feature Example

```yaml
image:
  # -- Traefik image host registry
  registry: docker.io
```

This feature is expected and therefore is defined clearly in the values.yaml file.

## Optional Feature Example

```yaml
# storage:
#   controlNode:
#     type: emptyDir
```

This feature is optional, non-critical, and therefore is commented out by default in the values.yaml file.

To allow this, template blocks that use this need to recursively test for existence of values before using them:

```yaml
{{- if .Values.storage}}
  {{- if .Values.storage.controlNode }}
    //code
    {{ .Values.storage.controlNode.type }}
  {{- end }}
{{- end }}
```

The non-critical feature defaults should be populated so that they can be enabled by simply uncommenting the section in the values.yaml file.

## Optional Non-Critical Feature Example

```yaml
# storage:
#   controlNode:
#     type: emptyDir
#     # (Optional)
#     # volume: 1Gi
```

The volume option is clearly optional, and non-critical. It is commented out (apart from the storage section comment block), and is also preceded by a comment of # (Optional) in the preceding line. This facilitates configuration, when the storage section is uncommented, the optional features are still disabled by default.

Similar to non-critical features, these options need to be tested for existence before use in the template.

Note
There can be optional values in critical features. These should just be added as an uncommented non-critical feature:

```yaml
image:
  name: traefik
  tag: 2.0.0
  # (Optional)
  # pullPolicy: IfNotPresent
```

Also, the first value under the primary value key does not require an optional comment:

```yaml
# ports:
#   http: 80
#   # (Optional)
#   # https: 443
```

This is because if the main subkey is not defined, the entirety of the feature is optional.

## Whitespace

Extra whitespace is to be avoided in templating. Conditionals should chomp whitespace:

```yaml
{{- if .Values }}
{{- end }}
```

There should be an empty commented line between each primary key in the values.yaml file to separate features from each other.

## Values YAML Design

The values.yaml file is designed to be user-friendly. It does not have to resemble the templated configuration if it is not conducive. Similarly, value names to not have to correspond to fields in the template if it is not conducive.
