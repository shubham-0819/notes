# Data structure in C

## Recursion:

Recursion is a programming technique where a function calls itself directly or indirectly. It's often used in solving problems that can be broken down into smaller, similar sub-problems.

Example: Factorial calculation using recursion.

```c
#include <stdio.h>

int factorial(int n) {
    if (n <= 1)
        return 1;
    else
        return n * factorial(n - 1);
}

int main() {
    int num = 5;
    printf("Factorial of %d = %d\n", num, factorial(num));
    return 0;
}
```

## Arrays:

Arrays are collections of elements of the same data type stored in contiguous memory locations.

Example: Finding the maximum element in an array.

```c
#include <stdio.h>

int main() {
    int arr[] = {10, 20, 30, 40, 50};
    int size = sizeof(arr) / sizeof(arr[0]);
    int max = arr[0];
    
    for (int i = 1; i < size; i++) {
        if (arr[i] > max)
            max = arr[i];
    }
    
    printf("Maximum element in the array: %d\n", max);
    
    return 0;
}
```

## Stack:

A stack is a data structure that follows the Last In, First Out (LIFO) principle. Elements are inserted and removed from the same end, called the top.

Example: Implementation of a stack using an array.

```c
#include <stdio.h>

#define MAX_SIZE 100

int stack[MAX_SIZE];
int top = -1;

void push(int item) {
    if (top == MAX_SIZE - 1) {
        printf("Stack Overflow\n");
        return;
    }
    stack[++top] = item;
}

int pop() {
    if (top == -1) {
        printf("Stack Underflow\n");
        return -1;
    }
    return stack[top--];
}

int main() {
    push(10);
    push(20);
    push(30);
    
    printf("Popped item: %d\n", pop());
    printf("Popped item: %d\n", pop());
    
    return 0;
}
```

## Queues:

A queue is a data structure that follows the First In, First Out (FIFO) principle. Elements are inserted from the rear and removed from the front.

Example: Implementation of a queue using an array.

```c
#include <stdio.h>

#define MAX_SIZE 100

int queue[MAX_SIZE];
int front = -1, rear = -1;

void enqueue(int item) {
    if (rear == MAX_SIZE - 1) {
        printf("Queue Overflow\n");
        return;
    }
    if (front == -1)
        front = 0;
    queue[++rear] = item;
}

int dequeue() {
    if (front == -1 || front > rear) {
        printf("Queue Underflow\n");
        return -1;
    }
    return queue[front++];
}

int main() {
    enqueue(10);
    enqueue(20);
    enqueue(30);
    
    printf("Dequeued item: %d\n", dequeue());
    printf("Dequeued item: %d\n", dequeue());
    
    return 0;
}
```

## Linked List:
A linked list is a linear data structure where elements are stored in nodes, and each node points to the next node in the sequence.

Example: Implementation of a singly linked list.

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *next;
};

struct Node *head = NULL;

void insert(int value) {
    struct Node *newNode = (struct Node *)malloc(sizeof(struct Node));
    newNode->data = value;
    newNode->next = NULL;
    if (head == NULL) {
        head = newNode;
        return;
    }
    struct Node *temp = head;
    while (temp->next != NULL)
        temp = temp->next;
    temp->next = newNode;
}

void display() {
    struct Node *temp = head;
    while (temp != NULL) {
        printf("%d -> ", temp->data);
        temp = temp->next;
    }
    printf("NULL\n");
}

int main() {
    insert(10);
    insert(20);
    insert(30);
    display();
    return 0;
}
```

## Trees:
A tree is a hierarchical data structure consisting of nodes connected by edges. It has a root node and zero or more child nodes.

Example: Implementation of a binary tree.

```c
#include <stdio.h>
#include <stdlib.h>

struct TreeNode {
    int data;
    struct TreeNode *left;
    struct TreeNode *right;
};

struct TreeNode *createNode(int value) {
    struct TreeNode *newNode = (struct TreeNode *)malloc(sizeof(struct TreeNode));
    newNode->data = value;
    newNode->left = NULL;
    newNode->right = NULL;
    return newNode;
}

int main() {
    struct TreeNode *root = createNode(1);
    root->left = createNode(2);
    root->right = createNode(3);
    root->left->left = createNode(4);
    root->left->right = createNode(5);
    return 0;
}
```

## Binary Search Trees:
A binary search tree (BST) is a binary tree in which the left child of a node has a value less than the node, and the right child has a value greater than the node.

Example: Insertion in a binary search tree.

```c
#include <stdio.h>
#include <stdlib.h>

struct TreeNode {
    int data;
    struct TreeNode *left;
    struct TreeNode *right;
};

struct TreeNode *createNode(int value) {
    struct TreeNode *newNode = (struct TreeNode *)malloc(sizeof(struct TreeNode));
    newNode->data = value;
    newNode->left = NULL;
    newNode->right = NULL;
    return newNode;
}

struct TreeNode *insert(struct TreeNode *root, int value) {
    if (root == NULL)
        return createNode(value);
    if (value < root->data)
        root->left = insert(root->left, value);
    else if (value > root->data)
        root->right = insert(root->right, value);
    return root;
}

void inorderTraversal(struct TreeNode *root) {
    if (root != NULL) {
        inorderTraversal(root->left);
        printf("%d ", root->data);
        inorderTraversal(root->right);
    }
}

int main() {
    struct TreeNode *root = NULL;
    root = insert(root, 50);
    insert(root, 30);
    insert(root, 20);
    insert(root, 40);
    insert(root, 70);
    insert(root, 60);
    insert(root, 80);
    inorderTraversal(root);
    return 0;
}
```

## Binary Heaps:
A binary heap is a complete binary tree that satisfies the heap property. It can be a min-heap or max-heap.

Example: Insertion in a min-heap.
```c
#include <stdio.h>
#include <stdlib.h>

#define MAX_SIZE 100

struct MinHeap {
    int *array;
    int size;
    int capacity;
};

struct MinHeap *createMinHeap(int capacity) {
    struct MinHeap *heap = (struct MinHeap *)malloc(sizeof(struct MinHeap));
    heap->capacity = capacity;
    heap->size = 0;
    heap->array = (int *)malloc(capacity * sizeof(int));
    return heap;
}

void insert(struct MinHeap *heap, int value) {
    if (heap->size == heap->capacity) {
        printf("Heap Overflow\n");
        return;
    }
    int i = heap->size;
    heap->array[i] = value;
    heap->size++;

    while (i > 0 && heap->array[i] < heap->array[(i - 1) / 2]) {
        // Swap parent and child
        int temp = heap->array[i];
        heap->array[i] = heap->array[(i - 1) / 2];
        heap->array[(i - 1) / 2] = temp;
        i = (i - 1) / 2;
    }
}

void display(struct MinHeap *heap) {
    for (int i = 0; i < heap->size; i++)
        printf("%d ", heap->array[i]);
    printf("\n");
}

int main() {
    struct MinHeap *heap = createMinHeap(MAX_SIZE);
    insert(heap, 3);
    insert(heap, 2);
    insert(heap, 1);
    insert(heap, 15);
    insert(heap, 5);
    insert(heap, 4);
    insert(heap, 45);
    display(heap);
    return 0;
}

```

## Graphs:

A graph is a collection of nodes (vertices) and edges that connect pairs of nodes. It can be directed or undirected.

Example: Implementation of an undirected graph using adjacency list.

``` c
#include <stdio.h>
#include <stdlib.h>

struct ListNode {
    int vertex;
    struct ListNode *next;
};

struct Graph {
    int numVertices;
    struct ListNode **adjLists;
};

struct ListNode *createNode(int vertex) {
    struct ListNode *newNode = (struct ListNode *)malloc(sizeof(struct ListNode));
    newNode->vertex = vertex;
    newNode->next = NULL;
    return newNode;
}

struct Graph *createGraph(int numVertices) {
    struct Graph *graph = (struct Graph *)malloc(sizeof(struct Graph));
    graph->numVertices = numVertices;
    graph->adjLists = (struct ListNode **)malloc(numVertices * sizeof(struct ListNode *));
    for (int i = 0; i < numVertices; i++)
        graph->adjLists[i] = NULL;
    return graph;
}

void addEdge(struct Graph *graph, int src, int dest) {
    struct ListNode *newNode = createNode(dest);
    newNode->next = graph->adjLists[src];
    graph->adjLists[src] = newNode;

    newNode = createNode(src);
    newNode->next = graph->adjLists[dest];
    graph->adjLists[dest] = newNode;
}

void display(struct Graph *graph) {
    for (int i = 0; i < graph->numVertices; i++) {
        struct ListNode *temp = graph->adjLists[i];
        printf("Adjacency list of vertex %d: ", i);
        while (temp != NULL) {
            printf("%d -> ", temp->vertex);
            temp = temp->next;
        }
        printf("NULL\n");
    }
}

int main() {
    struct Graph *graph = createGraph(4);
    addEdge(graph, 0, 1);
    addEdge(graph, 0, 2);
    addEdge(graph, 1, 2);
    addEdge(graph, 2, 3);
    display(graph);
    return 0;
}
```
