---
title: "Traverseする関数の書き方"
date: 2023-01-02T22:57:24+09:00
draft: false
tags:
  - "algorithm"
---

グラフを探索するコードを書くときに、base-caseをどこで判定すべきか迷うことがある。
例えば2分木をpre-orderで探索して各ノードが持つ値の合計を計算する関数traverseを書こうとするとき、以下3つの選択肢があるように思う。

<!--more-->

```c++
// 関数の先頭でbase-caseを判定
int traverse1(TreeNode* root){
    if(!root) return 0;
    int sum = root->val;
    sum += traverse(root->left);
    sum += traverse(root->right);
    return sum;
}

// 再帰呼び出しの前でbase-caseを判定
int traverse2(TreeNode* root){
    int sum = root->val;
    if(root->left) sum += traverse(root->left);
    if(root->right) sum += traverse(root->right);
    return sum;
}

// 関数の先頭と再帰呼び出しの前でbase-caseを判定
int traverse3(TreeNode* root){
    if(!root) return 0;
    int sum = root->val;
    if(root->left) sum += traverse(root->left);
    if(root->right) sum += traverse(root->right);
    return sum;
}
```

* traverse1が最もシンプルな書き方だろう。ただし、base-case判定に関数呼び出しが必要なため、base-case判定の計算コストが高い。また、base-caseの戻り値が自明でない場合、何かしらの設計が必要になる(例えばtraverseが子ノードの数を返す関数だった場合、そもそも親がnullのときは何を返せば良いのだろう？)。さらに、細かいが時間計算量を求めるために再帰呼び出しのツリーを可視化したときに、base-caseとそうでないときで計算量が異なるため、考えることが増える。
* traverse2はbase-caseを判定するときにわざわざ関数を呼び出さなくて済むため、base-case判定の計算コストが低い。また、base-caseで呼び出されることがないため、base-caseのときの戻り値を考えなくて済む。さらに、どんなときでも呼び出しあたりの計算量が等しいため、時間計算量を求めようとしたときに考えることが少ない。ただし、初回呼び出しのときに呼び出し側でbase-caseを判定しなければならない。
* tracerse3は1と2のハイブリッド案。

探索するグラフ・各ノードで行う処理・base-caseに合わせてどれを選択するか決めれば良いのだろう。私の経験的には、戻り値があり、base-caseのときの戻り値が自明でないときは2、その他の場合は1で書くと混乱せずに関数を書ききれる。少しでも計算コストを下げたいなら2。計算量解析は、まあ関数の詳細ではなく、その意図から考えるようにすればどの書き方であっても混乱しない。
