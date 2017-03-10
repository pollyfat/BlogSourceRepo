## install
#### MAC

    -> brew install graphviz 
    -> cpan
    -> install Graph::Easy
#### Windows
 - install graphviz([graphviz](http://www.graphviz.org/))
 - install perl([perl](http://www.perl.org/))
 - install cpan([cpan](http://www.cpan.org/modules/INSTALL.html))
 - install Graph::Easy by cpan

## Use

    graph-easy someDSL.txt
## Grammer
#### annotations

    # annotation
    #illegal annotation
    #FFFFFF # color
    \#transferred meaning
#### Node

    [single node]
    [node a],[node b]
#### Edges
 

    1. ->
    2. =>
    3. .>
    4. ~>
    5. .->
    6. ..->
    7. = >
    8. - >

add label:

    [ node a] - label ->[node b]
result:

    +--------+  request   +--------+
    | client | ---------> | server |
    +--------+            +--------+
#### Attributes

    {attribute: value}
   --------------
    
   > [官方文档](http://bloodgate.com/perl/graph/manual/index.html)
   > [中文文档](https://weishu.gitbooks.io/graph-easy-cn/content/overview/index.html)  ---  感谢weishu翻译