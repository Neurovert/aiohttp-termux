# Python aiohttp package for Termux (Android)

### DISCLAIMER

This is a **workaround**. I assume the real fix should happen directly in `c-ares`, but this was way faster and enough for my home assistant purposes.

Changes were made in order to be able to support homeassistant-2024.9.3 python package on Android.

If you want support for newer versions of HASS, you'd have to fix `uv` Python package support - currently it doesn't detect the interpreter correctly https://github.com/astral-sh/uv/issues/7373 https://github.com/astral-sh/uv/issues/2408

### Purpose

Version made as a workaround for the `Could not contact DNS servers` error stemming from `c-ares` incompatibility with Android.
It also fixes the SSL errors like:

```
2024-10-27_03:15:16.13684 2024-10-27 04:15:16.135 ERROR (MainThread) [metno] Access to https://aa015h6buqvih86i1.api.met.no/weatherapi/locationforecast/2.0/complete returned error 'ClientConnectorError'
2024-10-27_03:15:16.57953 2024-10-27 04:15:16.578 ERROR (MainThread) [homeassistant.components.homeassistant_alerts.coordinator] Error requesting homeassistant_alerts data: Cannot connect to host alerts.home-assistant.io:443 ssl:default [None]
...
2024-10-27_03:19:29.75595 Traceback (most recent call last):
2024-10-27_03:19:29.75597   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/resolver.py", line 104, in resolve
2024-10-27_03:19:29.75598     resp = await self._resolver.getaddrinfo(
2024-10-27_03:19:29.75599            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75600 aiodns.error.DNSError: (11, 'Could not contact DNS servers')
2024-10-27_03:19:29.75601
2024-10-27_03:19:29.75602 The above exception was the direct cause of the following exception:
2024-10-27_03:19:29.75603
2024-10-27_03:19:29.75604 Traceback (most recent call last):
2024-10-27_03:19:29.75605   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/connector.py", line 1301, in _create_direct_connection
2024-10-27_03:19:29.75606     hosts = await self._resolve_host(host, port, traces=traces)
2024-10-27_03:19:29.75608             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75609   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/connector.py", line 911, in _resolve_host
2024-10-27_03:19:29.75610     return await asyncio.shield(resolved_host_task)
2024-10-27_03:19:29.75614            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75615   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/connector.py", line 948, in _resolve_host_with_throttle
2024-10-27_03:19:29.75617     addrs = await self._resolver.resolve(host, port, family=self._family)
2024-10-27_03:19:29.75618             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75624   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/resolver.py", line 113, in resolve
2024-10-27_03:19:29.75625     raise OSError(msg) from exc
2024-10-27_03:19:29.75626 OSError: Could not contact DNS servers
2024-10-27_03:19:29.75627
2024-10-27_03:19:29.75628 The above exception was the direct cause of the following exception:
2024-10-27_03:19:29.75629
2024-10-27_03:19:29.75630 Traceback (most recent call last):
2024-10-27_03:19:29.75631   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiogithubapi/client.py", line 139, in async_call_api
2024-10-27_03:19:29.75632     result = await self._session.request(**request_arguments)
2024-10-27_03:19:29.75632              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75633   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/client.py", line 657, in _request
2024-10-27_03:19:29.75638     conn = await self._connector.connect(
2024-10-27_03:19:29.75639            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75640   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/connector.py", line 564, in connect
2024-10-27_03:19:29.75641     proto = await self._create_connection(req, traces, timeout)
2024-10-27_03:19:29.75642             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75642   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/connector.py", line 975, in _create_connection
2024-10-27_03:19:29.75644     _, proto = await self._create_direct_connection(req, traces, timeout)
2024-10-27_03:19:29.75645                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75645   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiohttp/connector.py", line 1307, in _create_direct_connection
2024-10-27_03:19:29.75647     raise ClientConnectorError(req.connection_key, exc) from exc
2024-10-27_03:19:29.75647 aiohttp.client_exceptions.ClientConnectorError: Cannot connect to host github.com:443 ssl:default [None]
2024-10-27_03:19:29.75648
2024-10-27_03:19:29.75649 The above exception was the direct cause of the following exception:
2024-10-27_03:19:29.75650
2024-10-27_03:19:29.75651 Traceback (most recent call last):
2024-10-27_03:19:29.75655   File "/data/data/com.termux/files/home/.homeassistant/custom_components/hacs/config_flow.py", line 98, in async_step_device
2024-10-27_03:19:29.75656     response = await self.device.register()
2024-10-27_03:19:29.75657                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75658   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiogithubapi/device.py", line 103, in register
2024-10-27_03:19:29.75659     response = await self._client.async_call_api(
2024-10-27_03:19:29.75660                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
2024-10-27_03:19:29.75661   File "/data/data/com.termux/files/home/hass/lib/python3.12/site-packages/aiogithubapi/client.py", line 141, in async_call_api
2024-10-27_03:19:29.75662     raise GitHubConnectionException(
2024-10-27_03:19:29.75663 aiogithubapi.exceptions.GitHubConnectionException: Request exception for 'https://github.com/login/device/code' with - Cannot connect to host github.com:443 ssl:default [None]
```

## Usage example with Home Assistant Core Python package

```bash
DNS_SERVERS=1.1.1.1,8.8.8.8 hass
```

# Installation on Termux

```bash
pip download aiohttp==3.10.5
tar -xzf aiohttp-3.10.5.tar.gz
cd aiohttp-3.10.5
curl -L https://github.com/Neurovert/aiohttp-termux/commit/722abe0a9250a836feb985a9154fd458ff149df6.patch -o fix.patch
patch -p1 < fix.patch
pip install .
```
