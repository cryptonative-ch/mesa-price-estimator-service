# Mesa Price Estimator Service
A tiny wiki for running Gnosis Protocol v1 price estimator

## Run the container

The price estimator run independently from the [Solver](https://docs.gnosis.io/protocol/docs/tutorial-solver/). To run it, run the following

```bash
docker run --env-file .env \
-v $PWD/stablex_orderbook_xdai.bin:/app/stablex_orderbook_xdai.bin \
-p 3000:8080 \
-d \
gnosispm/dex-price-estimator-rust:master
```
Where
- `stablex_orderbook_xdai.bin` is the orderbook recovery file to avoid fetching all Ethereum block since epoch
- `.env` file has the following

  ```bash
  RUST_BACKTRACE=1
  NODE_URL=<RPC-endpoint> # Any RPC endpoint for the xDAI network
  LOG_FILTER=warn,price_estimator=debug,services_core=debug,warp::filters::log=info
  NETWORK_ID=100 # xDAI
  RPC_TIMEOUT=60000 
  ORDERBOOK_FILE=/app/stablex_orderbook_xdai.bin # MUST match the same path
  TOKEN_DATA={}
  ECONOMIC_VIABILITY_SUBSIDY_FACTOR=1000
  ECONOMIC_VIABILITY_STRATEGY=Static
  STATIC_MIN_AVG_FEE_PER_ORDER=0
  STATIC_MAX_GAS_PRICE=100000000000
  NATIVE_TOKEN_ID=3
  GAS_ESTIMATORS=Web3
  ```
  
The container should now be running and can be accessed at (localhost:3000)[http://localhost:3000]
 
## Setup reverse proxy via Nginx
 
Create a nginx site with following configure

```nginx
server {
        listen 80;
        listen [::]:80;
        server_name price-estimator.dxdao.org;
        # Pass everything to Docker's
        location / {
           proxy_pass http://localhost:8138;
        }
}
server {
        # SSL Ports
        listen 443 ssl;
        listen [::]:443;
        # server name
        server_name price-estimator.dxdao.org;
        index index.html;
        location / {
                proxy_pass http://localhost:3000;
        }
        # SSL Certs
        ssl_certificate /etc/letsencrypt/live/price-estimator.dxdao.org/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/price-estimator.dxdao.org/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}
```


