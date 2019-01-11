# Introduction

*woocommerce2vuestorefront* is a data bridge that allows you to add all PWA features to your WooCommerce-based online store, including ultra-fast performance, home-screen access, push notifications, offline readiness and many more.

This indexer is a standalone application designed to feed Vue Storefront's catalog models with data right from WooCommerce.
 
Vue Storefront is a standalone PWA storefront, able to connect with WooCommerce through the API, without any changes on the backend. 

 ## Vue Storefront demo
 [![See how it works!](https://github.com/DivanteLtd/vue-storefront/raw/master/docs/.vuepress/public/Fil-Rakowski-VS-Demo-Youtube.png)](https://www.youtube.com/watch?v=L4K-mq9JoaQ)
Sign up for a dedicated demo with the Vue Storefront team at https://vuestorefront.io/

# Preparation 
Prior to using the *woocommerce2vuestorefront* indexer (which feeds the Vue Storefront database), you should install Vue Storefront. 
[Read the installation guide](https://divanteltd.github.io/vue-storefront/guide/installation/linux-mac.html).

# WooCommerce indexer
This project provides an indexer for *WooCommerce* data structures. The module is a standalone application that processes and indexes attributes, products, and categories for Elasticsearch Search Engine (Vue Storefront catalog model).

# Setup and installation
## Requirements 
- Node.js 8.2 or higher
- Running Elasticsearch 5.6+ (it's contained in the whole Vue Storefront API stack)
- Running WooCommerce (tested on version 3.5.3)

## Pre-configuration on WooCommerce side
1. Make sure that you have configured the REST API properly, and that the REST API is publicly accessible
2. Generate *consumer key* and *consumer secret* in the “advanced” tab in the WooCommerce settings (*read only* permissions are sufficient)
3. Having already installed the Vue Storefront API, you should build an index with a proper mapping for each type, you can achieve this by running the following command in the Vue Storefront API root directory:
`
nodejs scripts/db.js new
`

## Configure WooCommerce REST API connection
In `config.js` there is a *woo* section where you should adjust some data, i.e. keys for REST requests:
```
woo: {
    api: {
      host: 'localhost',
      protocol: 'http',
      auth: {
        consumer_key: "",
        consumer_secret: "",
        version: "v3"
      }
    }
  },
```
> NOTICE: the *version* should stay the same.

## Configure Elasticsearch connection

In the `db` section contained in the `config.js` file, you should adjust some info about Elasticsearch:
```
  db: {
    host: 'localhost',
    port: 9200,
    driver: 'elasticsearch',
    url: 'http://localhost:9200',
    indexName: 'vue_storefront_catalog'
  },
```
> NOTICE: the indexName can be changed but it should stay consistent with the Vue Storefront configuration. 

# Populate WooCommerce data in Elasticsearch

## Run indexers
Please execute indexers one by one with:
1. attributes: `nodejs cli.js attributes` 
2. categories: `nodejs cli.js categories`
3. products: `nodejs cli.js products`

and that's it. 

# For developers

## additional requirements
- docker
- docker-compose

## Run Wordpress with WooCommerce included (dockerized)

To simplify the development process, you may want to use one of the existing tools, for instance: ready-to-use docker images and `wp-cli`:

1. run containers by using `docker-compose.dev.yml` located in the `dev/docker/` subdirectory: `docker-compose -f docker-compose.dev.yml up`
> it's recommended to run this command from inside the `docker` dir, because of docker's networking naming conventions, which will be used in further steps.

2. install a fresh Wordpress instance: `docker run -it --rm --volumes-from vsf_woo --network docker_default wordpress:cli wp core install --url="localhost" --title="VSFvsWoo" --admin_user="admin" --admin_email="developer@company.com" --admin_password=admin`
> notice what parameters should be passed with the above 	command: network and volumes-from should cover names set in docker-compose.dev.yml
> it creates a new wordpress instance, available on `localhost` and with credentials `admin`/`admin`

3. install the WooCommerce plugin via wp-cli: `docker run -it --rm --volumes-from vsf_woo --network docker_default wordpress:cli wp plugin install woocommerce --activate`

4. add some attributes and options, by running:
```
   docker run -it --rm --volumes-from vsf_woo --network docker_default wordpress:cli wp wc product_attribute create --name=Size --type=options --user=admin
   docker run -it --rm --volumes-from vsf_woo --network docker_default wordpress:cli wp wc product_attribute_term create 1 --name=XS --user=admin
   docker run -it --rm --volumes-from vsf_woo --network docker_default wordpress:cli wp wc product_attribute_term create 1 --name=L --user=admin
```
> wp-cli command `wc product_attribute_term create` requires, as a first input, the ID of the attribute created during the first step (in this case `1`)
5. add some products via cli, or manually in wp-admin panel: 
```
docker run -it --rm --volumes-from vsf_woo --network docker_default wordpress:cli wp wc product create --name="Vue T-Shirt L" --type=simple --sku=VUE/T-shirt/L --regular_price=200 --user=admin --sale_price=150 --stock_quantity=94 --in_stock=true
```

> always pay attention to the arguments provided in the commands above, as they can vary depending on the Wordpress/WooCommerce current state.

# Credits
The woocommerce2vuestorefront module has been created by Divante's team, the core partner of Vue Storefront, including:
- Maciej Kucmus - @mkucmus

# Support
If you have any questions regarding this project, feel free to contact us:
- [E-mail](mailto:contributors@vuestorefront.io)
- [Slack](http://slack.vuestorefront.io)

# Licence 
The woocommerce2vuestorefront source code is completely free and released under the [MIT License](https://github.com/DivanteLtd/vue-storefront/blob/master/LICENSE).
