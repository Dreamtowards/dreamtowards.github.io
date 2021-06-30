

# LOD of Adaptive Dual Contouring. 
Posaed on Febarary 2, 2021.

LOD. Level of Detail，是实际网格构建中的一个主要主题之一。他将极大程度降低运算：网格碰撞，渲染，也很大程度提升空间性能。实际上不止于此。特别地，对于dc来说，由于支持 A.R. Adaptive Resolution 适应性分辨率，LOD将是一个极好的配合。

本文将描述LOD of dc的 基本实现概念，执行骨架，及一些实际上可能会遇到的问题与细节。

## basic Implemention Concepts. 



## Execution skeleton

```
doLOD(InternalNode intern, float untilsize) {
    for (int i: 0 -> 8) {
        if (intern.child[i].isInternal)
            intern.child[i] = doLOD(intern.child[i], untilsize);
    }
    if (intern.size <= untilsize) {
        LeafNode lodlf = new LeafNode(intern.min, .size);
        int n = 0;
        for (int i: 0 -> 8) {
            LeafNode c = intern.child[i];
            lodlf.sign(i, c!=null && c.sign(i));
            if (c != null && c.hasfp()) {
                lodlf.fp += c.fp;
                n++;
            }
        }
        lodlf.fp /= n;
        return avgfp;
    }
}
```


## Extra Problem and Details.


### Material sampling


### LOD-leaf 'wrongly' discard and cause some holes.