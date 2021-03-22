# bfs套路

+ 可以枚举出状态
+ 层层遍历

```java
class Solution {
    public int minDepth(TreeNode root) {
        if(root == null) {
            return 0;
        }

        Queue<TreeNode> queue = new LinkedList<>();
        queue.add(root);
        int level = 1;
        while(!queue.isEmpty()) {
            int size = queue.size();
            for(int i=0;i< size;i++) {
                TreeNode node = queue.poll();
                if(node.left == null && node.right == null) {
                    return level;
                }
                if(node.left != null) {
                    queue.offer(node.left);
                }
                if(node.right != null) {
                    queue.offer(node.right);
                }                
            }
            level ++;
        }
        return level;
    }
}
```


```java
class Solution {
    public int openLock(String[] deadends, String target) {
        Set<String> visited = new HashSet<>();
        Set<String> dead = new HashSet<>();

        for(String deadend : deadends) {
            dead.add(deadend);
        }
        visited.add("0000");
        Queue<String> queue = new LinkedList<>();
        queue.offer("0000");
        int cnt = 0;
        while(!queue.isEmpty()) {
            int size = queue.size();
            for(int i=0;i<size;i++) {               
                String password = queue.poll();
                if(password.equals(target)) {
                    return cnt;
                }
                if(dead.contains(password)) {
                    continue;
                }      
                for(int j=0;j<4;j++) {
                    // 向上
                    String up = up(password,j);
                    if(visited.add(up)) {
                        queue.offer(up);
                    }
                    // 向下
                    String down = down(password,j);
                    if(visited.add(down)) {
                        queue.offer(down);
                    }   
                }
            }
            cnt++;
        }
        return -1;
    }

    private String up(String s, int index) {
        char[] c = s.toCharArray();
        if(c[index] == '9') {
            c[index] = '0';
        }else {
            c[index]++;
        }
        return new String(c);
    }

    private String down(String s, int index) {
        char[] c = s.toCharArray();
        if(c[index] == '0') {
            c[index] = '9';
        }else {
            c[index]--;
        }
        return new String(c);
    }
}
```