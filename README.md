# Mesa Price Estimator Service
A tiny wiki for running Gnosis Protocol v1 price estimator

## Run the container

The price estimator run independently from the [Solver](https://docs.gnosis.io/protocol/docs/tutorial-solver/). To run it, run the following

```bash
docker run --env-file .env \
-v /var/apps/mesa-price-estimator/stablex_orderbook_xdai.bin:/app/stablex_orderbook_xdai.bin \
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
  WIP
 
  
