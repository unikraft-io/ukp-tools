# ukp-tools

The repository provides a set of helper scripts for the KraftCloud REST API to avoid having to manually craft `curl` command lines.

To use the scripts, setup the following environmental variables:
* `UKP_API_USER`: Your user name for KraftCloud
* `UKP_API_TOKEN`: Your KraftCloud authentication token
* `UKP_API_ADDR`: The URL to the REST API that you want to address with the scripts. Default: `https://api.fra0.kraft.cloud`

After setting up the variables, you can access the API endpoints using the scripts easily.
Some examples (Tip: use `jq` to pretty print the JSON response):

#### Creating a New Instance
To create a new instance you can change the `instances-create-tmpl.json` file to the desired configuration.
If you just run `./instances-create` (without arguments) it will pick up the file.

Alternatively, you can specify the configuration directly as argument like so (see the REST API documentation for the image configuration options):
```BASH
$ ./instances-create '{"image": "...", "memory_mb": 16, ... }' | jq
```
```JSON
{
  "status": "success",
  "data": {
    "instances": [
      {
        "uuid": "a32595ce-6028-4c6e-a342-1ba0c3cdf8af",
        "dns": "blue-flower-rr513cw9.fra0.kraft.cloud",
        "private_ip": "172.16.1.3/24",
        "boot_time_us": 9883,
        "status": "running"
      }
    ]
  }
}
```

#### List Your Instances
```BASH
$ ./instances-list | jq
```
```JSON
{
  "status": "success",
  "data": {
    "instances": [
      {
        "uuid": "a32595ce-6028-4c6e-a342-1ba0c3cdf8af"
      },
      {
        "uuid": "d725a5fc-e623-4d8a-81f3-629ab73fed8f"
      },
      {
        "uuid": "d3c12e4c-0120-4a9a-9ebe-c9bf0dcb67a0"
      }
    ]
  }
}
```

#### Get the Status of an Instance
```BASH
$ ./instances-status a32595ce-6028-4c6e-a342-1ba0c3cdf8af | jq
```
```JSON
{
  "status": "success",
  "data": {
    "instances": [
      {
        "status": "stopped",
        "uuid": "a32595ce-6028-4c6e-a342-1ba0c3cdf8af",
        "created_at": "2023-08-24T16:25:17Z",
        "image": "unikraft.io/...",
        "memory_mb": 16,
        "service_group": "193bc4d4-6104-45dc-83f0-ad68fdf7d3fd",
        "dns": "blue-flower-rr513cw9.fra0.kraft.cloud",
        "network_interfaces": [
          {
            "uuid": "6d83a078-ccaa-4137-aec6-bb9f8f50cd50",
            "private_ip": "172.16.1.3/24",
            "mac": "12:b0:ac:10:01:03"
          }
        ],
        "private_ip": "172.16.1.3/24",
        "boot_time_us": 9883
      }
    ]
  }
}
```

#### Delete all Instances
```BASH
$ ./instances-list > instances.txt
$ ./instances-delete many $(./trim-array instances.txt)
```
