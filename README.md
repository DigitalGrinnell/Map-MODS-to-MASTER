# Map-MODS-to-MASTER

```
[islandora@dgdocker1 ~]$ cat export.sh
Apache=isle-apache-dg
Target=/utility-scripts
# wget https://gist.github.com/McFateM/5bd7e5b0fa5d2928b2799d039a4c0fab/raw/collections.list
while read collection
do
    cp -f ri-query.txt query.sparql
    sed -i 's|COLLECTION|'${collection}'|g' query.sparql
    docker cp query.sparql ${Apache}:${Target}/${collection}.sparql
    rm -f query.sparql
    q=${Target}/${collection}.sparql
    echo Processing collection '${collection}'; Query is '${q}'...
    docker exec -w ${Target} ${Apache} mkdir -p exported-MODS/${collection}
    docker exec -w /var/www/html/sites/default/ ${Apache} drush -u 1 islandora_datastream_export --export_target=${Target}/exported-MODS/${collection} --query=${q} --query_type=islandora_datastream_exporter_ri_query  --dsid=MODS
done < collections.list
```
