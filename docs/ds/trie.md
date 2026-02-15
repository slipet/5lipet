# Trie

基本模板

```cpp
struct Node {
    Node* child[26]{};
    bool end = 0;
};
class Trie {
    Node* root = new Node();
    int find(string word) {
        Node* cur = root;
        for(auto &c: word) {
            c -= 'a';
            if(cur->child[c] == nullptr) {
                return 0;
            }
            cur = cur->child[c];
        }
        return cur->end ? 2 : 1;
    }
    void destroy(Node* node) {
        if(node == nullptr) {
            return;
        }
        for(Node* child: node->child) {
            destroy(child);
        }
        delete node;
    }
public:
    ~Trie() {
        destroy(root);
    }
    Trie() {
        
    }
    
    void insert(string word) {
        Node* cur = root;
        for(auto &c: word) {
            c -= 'a';
            if(cur->child[c] == nullptr) {
                cur->child[c] = new Node();
            }
            cur = cur->child[c];
        }
        cur->end = true;
    }
    
    bool search(string word) {
        return find(word) == 2;
    }
    
    bool startsWith(string prefix) {
        return find(prefix) != 0;
    }
};

```


### 子陣列最大異或和

使用 0/1 Trie