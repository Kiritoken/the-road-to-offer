# LRU


```java
class LRUCache {

    private Map<Integer,Node> dict;

    private int capacity;

    private Node first;

    private Node last;

    public LRUCache(int capacity) {
        this.dict = new HashMap<>(capacity);
        this.capacity = capacity;
        this.first = new Node();
        this.last = new Node();
        //
        this.first.next = this.last;
        this.last.prev = this.first;
    }
    
    public int get(int key) {
        // 1. 从字典中查出Node
        Node  node  = dict.get(key);
        if(node == null) {
            return -1;
        }
        // unlink
        unlink(node);
        // linkFirst
        linkFirst(node);
        return node.value;
    }
    
    public void put(int key, int value) {
        Node node = dict.get(key);
        if(node == null) {
            node = new Node(null,key,value,null);
        }else {
            node.value = value;
            unlink(node);
        }
        linkFirst(node);
        this.dict.put(key,node);
        
        // 淘汰last前的最后一个
        if(dict.size() > capacity) {
            dict.remove(last.prev.key);
            unlink(last.prev);
        }

    }


    private void linkFirst(Node node) {
        node.next = first.next;
        first.next.prev = node;
        node.prev = first;
        first.next = node; 
    }

    private void unlink(Node node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
        node.next = null;
        node.prev = null; // help gc
    }

}

 class Node {
     int key;
     int value;
     Node prev;
     Node next;

     public Node(Node prev,int key, int value , Node next) {
         this.prev= prev;
         this.key = key;
         this.value = value;
         this.next = next;
     }

     public Node() {

     }
 }
```