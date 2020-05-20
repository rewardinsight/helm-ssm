# helm-ssm
A low-dependency tool used to retrieves and injects secrets from AWS SSM Parameter Store.

## Installation
```bash
$ helm plugin install https://github.com/rewardinsight/helm-ssm
```

## Overview
This plugin provides the ability to encode AWS SSM parameter paths into your
value files to store in version control or just generally less secure places.

During installation or upgrade, the parameters are replaced with their actual values
and passed on to Tiller.

Usage:
Simply use helm as you would normally, but add 'ssm' before any command,
the plugin will automatically search for values with the pattern:
```
{{ssm /path/to/parameter aws-region}}
```
and replace them with their decrypted value.
>Note: You must have IAM access to the parameters you're trying to decrypt, and their KMS key.

>Note #2: Wrap the template with quotes, otherwise helm will confuse the brackets for json, and will fail rendering.

>Note #3: Currently, helm-ssm does not work when the value of the parameter is in the default chart values.

E.g:
```bash
$ helm ssm install stable/docker-registry --values value-file1.yaml -f value-file2.yaml
```

value-file1.yaml:
```
secrets:
  haSharedSecret: "{{ssm /mgmt/docker-registry/shared-secret us-east-1}}"
  htpasswd: "{{ssm /mgmt/docker-registry/htpasswd us-east-1}}"
```


##Using a Prefix
If your SSM parameters have a preset you can specify it at run time using the -p or --prefix flags followed by a string

E.g:
helm ssm install stable/docker-registry --values value-file1.yaml -f value-file2.yaml -p "/some/prefix/path"

##Overriding the region
If your wantr to globally override the region setting given in each param string you can specify it at run time using 
the -r or --region flags followed by a region string e.g. eu-west-1

E.g:
helm ssm install stable/docker-registry --values value-file1.yaml -f value-file2.yaml -p "/some/prefix/path" -r "eu-west-1"

## Testing
```
$ ./ssm.sh install tests/testchart/ --debug --dry-run -f tests/testchart/values.yaml
```
