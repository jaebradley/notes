# [Learning Tarjan's algorithm](https://dwf.dev/blog/2024/09/25/2024/learning-tarjans-algorithm/)
* Strongly connected components are self-contained cycles within a directed graph where every vertex in a given cycle can reach every other vertex in the same cycle
* These cycles can be "collapsed" into different vertices that represent the entire component

<img width="546" height="191" alt="image" src="https://github.com/user-attachments/assets/609154e0-a41f-46da-ab48-c794fd6185b5" />

* Edges between super-vertices: `ABF` -> `GH`, `CDI` -> `GH` and `CDI` -> `ABF`, and `E` -> `E` and `E` -> `CDI`
* 
