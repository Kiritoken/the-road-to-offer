# LFU
```java
public class LFUCache {

    private int capacity;

    private TreeSet<Node> freqDict;

    private Map<Integer, Node> map;

    private int time;

    public LFUCache(int capacity) {
        this.capacity = capacity;
        this.freqDict = new TreeSet<>();
        this.map = new HashMap<>(capacity);
        this.time = 0;
    }

    public int get(int key) {
        if (!map.containsKey(key)) {
            return -1;
        } else {
            Node node = map.get(key);
            freqDict.remove(node);
            // 更新
            node.updateTime = ++time;
            node.freq++;
            freqDict.add(node);
            map.put(key,node);
            return node.value;
        }
    }

    public void put(int key, int value) {
        if(capacity <= 0) {
            return;
        }
        if (map.containsKey(key)) {
            // 存在
            Node node = map.get(key);
            freqDict.remove(node);
            node.freq++;
            node.updateTime = ++time;
            node.value = value;
            freqDict.add(node);
            map.put(key, node);
        } else {
            // 不存在
            if (freqDict.size() >= capacity) {
                map.remove(freqDict.first().key);
                freqDict.remove(freqDict.first());
            }
            Node node = new Node(key, value, 1, ++time);
            freqDict.add(node);
            map.put(key, node);
        }


    }


    static class Node implements Comparable<Node> {

        public int key;
        public int value;
        public int updateTime;
        public int freq;


        public Node(int key, int value, int freq, int updateTime) {
            this.key = key;
            this.value = value;
            this.freq = freq;
            this.updateTime = updateTime;
        }


        @Override
        public int compareTo(Node o) {
            return freq == o.freq ?  updateTime - o.updateTime : freq - o.freq;
        }

        @Override
        public int hashCode() {
            return key >>> 16 + updateTime;
        }

        @Override
        public boolean equals(Object obj) {
            if (obj == this) {
                return true;
            }

            if (obj == null) {
                return false;
            }
            if (obj instanceof Node) {
                Node another = (Node) obj;
                return this.updateTime == another.updateTime && this.key == another.key;
            }
            return false;
        }
    }
}
```