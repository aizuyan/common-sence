cat t.txt |jq  '.aggregations.client_province.buckets | \(.[].key) \(.[].total.value)'
