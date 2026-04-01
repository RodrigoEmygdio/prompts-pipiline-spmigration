```
{
  "spId": "SP_XYZ",
  "chunks": [
    {
      "chunkId": "chunk-01",
      "type": "declarations",
      "startLine": 1,
      "endLine": 42
    },
    {
      "chunkId": "chunk-02",
      "type": "if_block",
      "startLine": 43,
      "endLine": 118
    }
  ]
}
```
Arquivo: work/<SP_ID>/10-prepared/sp-chunks.json

### Codegen com reuso do codebase
* contexto indexado do codebase + política explícita de reuso
* decidir entre reutilizar, estender ou criar novo
