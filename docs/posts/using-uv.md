---
draft: false
date: 2025-05-05
authors:
  - me
categories:
  - Python
---

# Using UV
This post covers how I configure and use UV appropriately. Some of these tips are specifically useful within a corporate network.<!-- more -->

## Setting Environment Variables
Set these defaults:

```
export UV_DEFAULT_INDEX=https://my-internal-pypi-upstream.corp/pypi
export UV_INDEX=https://my-internal-pypi.corp/pypi-internal
export UV_NATIVE_TLS=true
export UV_PYTHON=3.12
```

### UV_DEFAULT_INDEX & UV_INDEX
[UV_DEFAULT_INDEX](https://docs.astral.sh/uv/configuration/environment/#uv_default_index) configures UV to use this url as the default index for package installation. Typically in corporate environments, there will be some local pypi server you'll need to use. This is the setting to configure it as the default.

[UV_INDEX](https://docs.astral.sh/uv/configuration/environment/#uv_index) is a space seperated list of index URLs where UV may search for packages. Typically in a corporate environment, this would be the index url where internally developed libraries are kept.

### UV_NATIVE_TLS
[UV_NATIVE_TLS](https://docs.astral.sh/uv/configuration/environment/#uv_native_tls) configures UV to use the system's trust store. Important on corporate environments that employ SSL decrypt.

### UV_PYTHON
[UV_PYTHON](https://docs.astral.sh/uv/configuration/environment/#uv_native_tls) lets you configure the default python version to use. By default, UV will use the latest available. This became problematic when [Python 3.13 changed](https://stackoverflow.com/questions/79123649/does-python-3-13-ignore-ssl-cert-file-environment-variable-by-design) how it sets the ssl default_context. For now, I default all of my venvs to 3.12.