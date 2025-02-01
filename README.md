**Using this repo temporarily to fix Home Assistant IPv6 connectivity issues:**

```sh
mkdir -p /etc/cont-init.d
cat <<EOF > /etc/cont-init.d/00-replace-aionanoleaf-ipv6.sh
#!/usr/bin/with-contenv bashio
uv pip install --force-reinstall git+https://github.com/janw/aionanoleaf.git
EOF
```

**If you're running HA in kubernetes:**

```yaml
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: homeassistant-custom-init
data:
  00-replace-aionanoleaf-ipv6.sh: |
    #!/usr/bin/with-contenv bashio
    uv pip install --force-reinstall git+https://github.com/janw/aionanoleaf.git

---
# On the deployment/statefulset:
# …
          volumeMounts:
            - mountPath: /etc/cont-init.d
              name: homeassistant-custom-init

# …
      volumes:
        - name: homeassistant-custom-init
          configMap:
            name: homeassistant-custom-init
            defaultMode: 0777
```

----

# aioNanoleaf package

[![PyPI](https://img.shields.io/pypi/v/aionanoleaf)](https://pypi.org/project/aionanoleaf/) ![PyPI - Downloads](https://img.shields.io/pypi/dm/aionanoleaf) [![PyPI - License](https://img.shields.io/pypi/l/aionanoleaf?color=blue)](https://github.com/milanmeu/aionanoleaf/blob/main/COPYING)

An async Python wrapper for the Nanoleaf API.

## Installation
```bash
pip install aionanoleaf
```

## Usage
### Import
```python
from aionanoleaf import Nanoleaf
```

### Create a `aiohttp.ClientSession` to make requests
```python
from aiohttp import ClientSession
session = ClientSession()
```

### Create a `Nanoleaf` instance
```python
from aionanoleaf import Nanoleaf
light = Nanoleaf(session, "192.168.0.100")
```

## Example
```python
from aiohttp import ClientSession
from asyncio import run

import aionanoleaf

async def main():
    async with ClientSession() as session:
        nanoleaf = aionanoleaf.Nanoleaf(session, "192.168.0.73")
        try:
            await nanoleaf.authorize()
        except aionanoleaf.Unauthorized as ex:
            print("Not authorizing new tokens:", ex)
            return
        await nanoleaf.turn_on()
        await nanoleaf.get_info()
        print("Brightness:", nanoleaf.brightness)
        await nanoleaf.deauthorize()
run(main())
```
