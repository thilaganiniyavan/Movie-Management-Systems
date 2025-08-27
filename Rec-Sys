from collections import defaultdict
import heapq


# ------------------------ TRIE IMPLEMENTATION ------------------------ #
class TrieNode:
    """Node for Trie structure. Holds children references and end-of-word flag."""
    def __init__(self):
        self.children = [None] * 26  # references to next letters
        self.last = False            # flag for end of word

    def get_index(self, char):
        """Convert a character into an index (0-25)."""
        return ord(char.lower()) - ord('a')


class Trie:
    """Trie data structure for autocomplete functionality."""
    def __init__(self):
        self.root = TrieNode()

    def form_trie(self, keys):
        """Insert a list of words/keys into the Trie."""
        for key in keys:
            self.insert(key)

    def insert(self, key):
        """Insert a single word into the Trie."""
        node = self.root
        for char in key:
            if char.isalpha():
                index = node.get_index(char)
                if not node.children[index]:
                    node.children[index] = TrieNode()
                node = node.children[index]
        node.last = True

    def _suggestions_rec(self, node, word, suggestions):
        """Recursively collect all suggestions below a given Trie node."""
        if node.last:
            suggestions.append(word)
        for i in range(26):
            if node.children[i]:
                self._suggestions_rec(node.children[i], word + chr(i + ord('a')), suggestions)

    def auto_suggestions(self, key):
        """Return list of words that match a given prefix."""
        node = self.root
        for char in key:
            if char.isalpha():
                index = node.get_index(char)
                if not node.children[index]:
                    return []
                node = node.children[index]

        if not any(node.children):
            return []

        suggestions = []
        self._suggestions_rec(node, key, suggestions)
        return suggestions


# -------------------- PRIORITY QUEUE (MAX HEAP) -------------------- #
class PriorityQueue:
    """Custom max-priority queue using binary heap operations."""
    def __init__(self):
        self.elements = []

    def put(self, item, priority):
        """Insert item with priority."""
        self.elements.append((priority, item))
        self._bubble_up(len(self.elements) - 1)

    def _bubble_up(self, index):
        """Ensure heap property from bottom to top."""
        parent_index = (index - 1) // 2
        if index > 0 and self.elements[index][0] > self.elements[parent_index][0]:
            self.elements[index], self.elements[parent_index] = self.elements[parent_index], self.elements[index]
            self._bubble_up(parent_index)

    def get(self):
        """Remove and return the element with the highest priority."""
        if not self.elements:
            return None
        self.elements[0], self.elements[-1] = self.elements[-1], self.elements[0]
        item = self.elements.pop()
        self._bubble_down(0)
        return item[1]

    def _bubble_down(self, index):
        """Ensure heap property from top to bottom."""
        left, right = 2 * index + 1, 2 * index + 2
        largest = index

        if left < len(self.elements) and self.elements[left][0] > self.elements[largest][0]:
            largest = left
        if right < len(self.elements) and self.elements[right][0] > self.elements[largest][0]:
            largest = right

        if largest != index:
            self.elements[index], self.elements[largest] = self.elements[largest], self.elements[index]
            self._bubble_down(largest)

    def is_empty(self):
        return not self.elements


# ------------------------- AVL TREE ------------------------- #
class AVLNode:
    """Node for AVL tree, storing key-value with height and children."""
    def __init__(self, key, value):
        self.key = key
        self.value = value
        self.height = 1
        self.left = None
        self.right = None


class AVLTree:
    """Self-balancing AVL Tree for efficient sorted storage."""
    def __init__(self):
        self.root = None

    def insert(self, root, key, value):
        """Insert key-value pair into AVL tree with balancing."""
        if not root:
            return AVLNode(key, value)
        elif key < root.key:
            root.left = self.insert(root.left, key, value)
        else:
            root.right = self.insert(root.right, key, value)

        root.height = 1 + max(self.get_height(root.left), self.get_height(root.right))
        balance = self.get_balance(root)

        # Rotations to maintain balance
        if balance > 1 and key < root.left.key:
            return self.right_rotate(root)
        if balance < -1 and key > root.right.key:
            return self.left_rotate(root)
        if balance > 1 and key > root.left.key:
            root.left = self.left_rotate(root.left)
            return self.right_rotate(root)
        if balance < -1 and key < root.right.key:
            root.right = self.right_rotate(root.right)
            return self.left_rotate(root)

        return root

    def left_rotate(self, z):
        """Left rotation operation."""
        y, T2 = z.right, z.right.left
        y.left, z.right = z, T2
        z.height = 1 + max(self.get_height(z.left), self.get_height(z.right))
        y.height = 1 + max(self.get_height(y.left), self.get_height(y.right))
        return y

    def right_rotate(self, z):
        """Right rotation operation."""
        y, T3 = z.left, z.left.right
        y.right, z.left = z, T3
        z.height = 1 + max(self.get_height(z.left), self.get_height(z.right))
        y.height = 1 + max(self.get_height(y.left), self.get_height(y.right))
        return y

    def get_height(self, root):
        return root.height if root else 0

    def get_balance(self, root):
        return self.get_height(root.left) - self.get_height(root.right) if root else 0

    def get_in_order(self, root, result):
        """In-order traversal to get ascending order of elements."""
        if root:
            self.get_in_order(root.left, result)
            result.append((root.key[1], root.key[0]))
            self.get_in_order(root.right, result)

    def get_reverse_order(self, root, result):
        """Reverse in-order traversal to get descending order of elements."""
        if root:
            self.get_reverse_order(root.right, result)
            result.append((root.key[1], root.key[0]))
            self.get_reverse_order(root.left, result)


# -------------------- MOVIE RATINGS -------------------- #
class MovieRatings:
    """Manage movie ratings using an AVL Tree for efficient sorting."""
    def __init__(self):
        self.ratings = defaultdict(lambda: [0, 0])  # movie -> [sum, count]
        self.avl_tree = AVLTree()

    def add_rating(self, movie, rating):
        """Add a rating to a movie and update average rating in AVL Tree."""
        total, count = self.ratings[movie]
        self.ratings[movie] = [total + rating, count + 1]
        avg_rating = (total + rating) / (count + 1)
        self.avl_tree.root = self.avl_tree.insert(self.avl_tree.root, (avg_rating, movie), movie)

    def get_movies_in_order(self):
        """Return movies sorted by rating ascending."""
        result = []
        self.avl_tree.get_in_order(self.avl_tree.root, result)
        return result

    def get_movies_in_reverse_order(self):
        """Return movies sorted by rating descending."""
        result = []
        self.avl_tree.get_reverse_order(self.avl_tree.root, result)
        return result

    def get_movies_with_rating(self, target_rating):
        """Return list of movies matching a specific rating."""
        result = []
        self.avl_tree.get_in_order(self.avl_tree.root, result)
        return [movie for movie, rating in result if rating == target_rating]


# -------------------- SEGMENT TREE -------------------- #
class SegmentTree:
    """Segment Tree for range sum queries and updates."""
    def __init__(self, arr):
        self.n = len(arr)
        self.tree = [0] * (2 * self.n)
        self.build(arr)

    def build(self, arr):
        """Build segment tree from input array."""
        for i in range(self.n):
            self.tree[self.n + i] = arr[i]
        for i in range(self.n - 1, 0, -1):
            self.tree[i] = self.tree[2 * i] + self.tree[2 * i + 1]

    def update(self, index, value):
        """Update element at index with new value."""
        pos = index + self.n
        self.tree[pos] = value
        while pos > 1:
            pos //= 2
            self.tree[pos] = self.tree[2 * pos] + self.tree[2 * pos + 1]

    def query(self, l, r):
        """Range sum query between l and r inclusive."""
        l += self.n
        r += self.n
        res = 0
        while l <= r:
            if l % 2 == 1:
                res += self.tree[l]
                l += 1
            if r % 2 == 0:
                res += self.tree[r]
                r -= 1
            l //= 2
            r //= 2
        return res


# -------------------- GRAPH -------------------- #
class Graph:
    """Graph implemented with adjacency list, supporting BFS and DFS."""
    def __init__(self):
        self.graph = defaultdict(list)

    def add_edge(self, u, v):
        """Add an edge from node u to v."""
        self.graph[u].append(v)

    def bfs(self, start):
        """Perform Breadth-First Search starting from node start."""
        visited = set()
        queue = [start]
        result = []
        while queue:
            node = queue.pop(0)
            if node not in visited:
                visited.add(node)
                result.append(node)
                queue.extend(self.graph[node])
        return result

    def dfs_util(self, node, visited, result):
        visited.add(node)
        result.append(node)
        for neighbor in self.graph[node]:
            if neighbor not in visited:
                self.dfs_util(neighbor, visited, result)

    def dfs(self, start):
        """Perform Depth-First Search starting from node start."""
        visited = set()
        result = []
        self.dfs_util(start, visited, result)
        return result


# -------------------- USER CLASS -------------------- #
class User:
    """User class holding username and watched movies list."""
    def __init__(self, username):
        self.username = username
        self.watched = []

    def watch(self, movie):
        """Add movie to user's watched list."""
        self.watched.append(movie)

    def get_watched(self):
        """Return list of watched movies."""
        return self.watched


# ------------------------- DRIVER CODE WITH MENU ------------------------- #
def main_menu():
    print("\n==== MAIN MENU ====")
    print("1. Search movie titles (Trie)")
    print("2. Manage movie ratings (AVL Tree)")
    print("3. Segment tree demo")
    print("4. Graph traversal demo")
    print("5. User watchlist demo")
    print("0. Exit")
    choice = input("Enter your choice: ").strip()
    return choice


if __name__ == "__main__":
    # Preloaded titles/genres for Trie demo
    keys = [
        "Stranger Things", "Breaking Bad", "Better Call Saul",
        "Black Mirror", "Bridgerton", "Batman Begins",
        "Avengers", "Avatar", "Action", "Adventure", "Animation"
    ]

    while True:
        choice = main_menu()

        if choice == "1":
            prefix = input("Enter prefix to search: ").strip()
            print(f"User searches for titles starting with '{prefix}'")
            trie = Trie()
            trie.form_trie(keys)
            suggestions = trie.auto_suggestions(prefix)
            if not suggestions:
                print("No title or genre found with this prefix\n")
            else:
                print("Suggestions:")
                for s in suggestions:
                    print(" -", s)

        elif choice == "2":
            movie_ratings = MovieRatings()
            m = int(input("Enter number of movie ratings to add: "))
            for _ in range(m):
                movie, rating = input("Enter movie name and rating: ").split()
                movie_ratings.add_rating(movie, int(rating))

            print("Movies sorted by average rating (ascending):", movie_ratings.get_movies_in_order())
            print("Movies sorted by average rating (descending):", movie_ratings.get_movies_in_reverse_order())

            target = int(input("Enter a rating to filter movies: "))
            print(f"Movies with rating {target}:", movie_ratings.get_movies_with_rating(target))

        elif choice == "3":
            arr = [1, 3, 5, 7, 9, 11]
            seg_tree = SegmentTree(arr)
            print("Segment Tree range sum (1,3):", seg_tree.query(1, 3))
            seg_tree.update(1, 10)
            print("Segment Tree range sum (1,3) after update:", seg_tree.query(1, 3))

        elif choice == "4":
            g = Graph()
            g.add_edge(0, 1)
            g.add_edge(0, 2)
            g.add_edge(1, 2)
            g.add_edge(2, 0)
            g.add_edge(2, 3)
            g.add_edge(3, 3)
            print("Graph BFS starting from 2:", g.bfs(2))
            print("Graph DFS starting from 2:", g.dfs(2))

        elif choice == "5":
            user = User("Alice")
            user.watch("Stranger Things")
            user.watch("Breaking Bad")
            print(f"{user.username} watched:", user.get_watched())

        elif choice == "0":
            print("Exiting program. Goodbye!")
            break

        else:
            print("Invalid choice. Please try again.")
