### Consul 服务治理

	Download : https://www.consul.io/downloads.html

	Tool : https://www.consul.io/downloads_tools.html

	Getting started: https://learn.hashicorp.com/consul/#getting-started
	
	
	blog : 
		https://www.cnblogs.com/wyt007/p/10631109.html
		https://www.cnblogs.com/xiandnc/p/9374533.html
		https://www.cnblogs.com/jesse2013/p/net-core-apigateway-ocelot-docs.html
	    https://www.cnblogs.com/lhxsoft/p/10637276.html

>>> config
web.json
{
    "service": {
        "name": "web",
        "tags": [
            "rails"
        ],
        "port": 8888
    }
}


>>> Starting the Agent

$ consul agent -dev

Cluster Members : members of the Consul cluster

$ consul members
$ consul members -detailed

$ curl localhost:8500/v1/catalog/nodes

Stopping the Agent

Ctrl-C


consul agent -server -datacenter=dc1 -bootstrap -data-dir ./data -config-file ./conf -ui-dir ./dist -node=n1 -bind=192.168.64.1