
# Starsh elastic search first
docker compose up -d opensearch
docker compose up -d opensearch-dashboards

# apply mappinf for elastic search
open elastic search : http://localhost:5601/
go to Dev Tools menu then put this mapping

```json
PUT _index_template/nginx_elastic_stack_example
{
  "index_patterns": ["nginx-*"],
  "template": {
    "settings": {
      "index.refresh_interval": "5s"
    },
    "mappings": {
      "dynamic_templates": [
        {
          "message_field": {
            "match": "message",
            "match_mapping_type": "string",
            "mapping": {
              "type": "text",
              "norms": false
            }
          }
        },
        {
          "string_fields": {
            "match": "*",
            "match_mapping_type": "string",
            "mapping": {
              "type": "text",
              "norms": false,
              "fields": {
                "raw": {
                  "type": "keyword"
                }
              }
            }
          }
        }
      ],
      "properties": {
        "geoip": {
          "dynamic": true,
          "properties": {
            "location": {
              "type": "geo_point"
            }
          }
        },
        "bytes": {
          "type": "float"
        },
        "request": {
          "type": "keyword"
        }
      }
    }
  },
  "priority": 500,
  "version": 1,
  "_meta": {
    "description": "Template for nginx logs with geoip and string field normalization"
  }
}

```

# start logstash and file beat
docker compose up -d logstash  
docker compose up -d filebeat
