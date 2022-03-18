## Overview

as discribed the projects bases of backend and separated frontend. The file system structure represents this.

```graphviz
digraph hierarchy {
  // https://www.tonyballantyne.com/graphs.html
  rankdir=LR;
  // nodesep=1.0 // Increases the separation between nodes
  // node [color=Red,fontname=Courier,shape=box] // Default node style
  // edge [color=Blue, style=dashed] // Default lins style 

  node [color=Red,shape=none] // Default node style
  edge [color=grey, style=dashed] // Default lins style 


  project->{backend frontend}
  backend
  frontend->{search submit}
  {rank=same; search submit} // Put them on the same level
}
```

Whilst the backend runs on clojure the frontend is modeled in nodejs quering data unsing graphql-interface. 

```mermaid
flowchart LR;
    classDef focus fill:#f96;
    classDef hide_ color:gray , stroke-dasharray: 5 5 , stroke:lightgray;
     subgraph backend
        subgraph clojure
            direction TB
            I(import):::hide_ -.- B(backend):::focus
            B -.- e(export):::hide_
        end
    end
    subgraph frontend
        subgraph nodejs
            direction LR
            D(search):::focus
            E(submit):::hide_
        end
    end
    backend ==graphql==> frontend
    style clojure color:grey;
    style nodejs color:grey;
    linkStyle 0 stroke:lightgrey;
    linkStyle 1 stroke:lightgrey;
```
The import and export function currently tied to the backend. Data to import will be pipelined from the source to interal database (rocksdb). Multiple steps allow imports to be adjusted. The connector gathers data from source (APIs, crawling for HTML/Mail) or supplied files. 

```mermaid
flowchart LR;
    classDef focus fill:#f96;
    subgraph connector
        direction LR
        c_a(REST-api) --> d 
        c_c(HTML crawler)--> d
        c_m(mail crawler) --> d(data):::focus
    end
    subgraph import
        direction LR
        c(csv) --> o(object):::focus
        e(edn) --> o
        j(json) --> o
        xlm(xlm) --> o
        xlsx(xlsx) --> o
        o --> I(mapping):::focus
    end
    subgraph backend
         db[(db)]:::focus 
    end
        c_f(file) --> import
    connector --> import
    import --> backend
```

Delivered formats are loaded into objects. In general we expect 2 dimensional data. **First step - Reading** A file contains multiple rows. Each row is a data point. Each row has multiple values. Imports as CSV support headlines. **Second step - Mapping** The internal 2-dimensional data grid is mapped with help of associative attribut naming. Further rules, checks and specialised functions are added - see 'offer_mapping'. This allows to handle customer specific representation of boolean values or time string.




