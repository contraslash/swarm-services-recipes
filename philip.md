# Philip
```
docker service create \
  --network traefik-network \
  --name philip \
  --label traefik.port=9000 \
  --env RASA_MITIE_FILE='/nlu_models/MITIE_models/spanish/total_word_feature_extractor.dat'
  contraslash/finantic-philip
```
