---
layout:         post
title:          "Mergesort, Explained"
date:           2020-06-02 17:53:12 -0500
categories:     jekyll update
---

<link href="https://fonts.googleapis.com/css2?family=Quicksand&display=swap" rel="stylesheet">
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
<div style="font-family: Quicksand, sans-serif; ">
  <h1 style="font-size: 40px;"> Mergesort: the Algorithm </h1>
  Sorting algorithms are so important that I though it would be worth it to take a look at a very common and efficient sorting algorithm: Mergesort. Merge sort is a divide-and-conquer algorithm. By that, we mean that we divide the problem into many subproblems, and then combine the solutions to these subproblems in order to get the solution to our initial problem. If that doesn't make much sense, let's get right into it.

  Suppose we want to sort the following array of numbers: [5, 2, 4, 7, 1, 3, 2, 6]

  The merge sort algorithm is recursive, so what it says is: divide this list into two sorted arrays (each of length half the original array), and then merge them such that you get a sorted array.

  Here's the pseudocode:
  {% highlight pseudo %}
  Mergesort(A, p, r)
    q = (p + r)/2
    mergesort(A, p, q)
    mergesort(A, q+1, r)
    merge(A, p, q, r)
  {% endhighlight %}

  Now you might ask, how can you divide this unsorted array into 2 sorted array? Well, the answer is that right away, you can't. This is why you divide the array into two "unsorted" sub-arrays and apply mergesort to each of these two sub-arrays.

  Hence, you divide each of the two sub-arrays again. You keep doing that until you are left with only arrays of length 1. The process looks like this:
  <br>
  <br>
  <img src="/images/mergesort_divide.svg">
  <br>
  <br>






  Then, once we have reached the stage where we have a bunch of arrays of size 1, we can start merging them, all the way up, until we get our sorted array. If this sounds unclear, take a look at the following picture:


  <br>
  <br>

  <img src="/images/mergesort_merge.svg">
  <br>
  <br>


  Here's the C++ code that does the same thing for the vector v:
  {% highlight C++ %}
  std::vector<int> sort(std::vector<int> v, int l, int r) {
    // base case: if you reach a vector of size 1, just return this vector as it is already sorted.
    if (l >= r) {
      std::vector<int> v1;
      v1.push_back(v[r]);
      return v1;
    }
    // divide the vector into 2 sub vectors
    int mid = (l+r)/2;
    // sort the first subvector
    std::vector<int> a = sort(v, l, mid);
    // sort the second subvector
    std::vector<int> b = sort(v, mid + 1, r);
    // merge the two subvectors and return the result
    std::vector<int> result = merge(a, b);
    return result;
  }
  {% endhighlight %}

  <h1 style="font-size: 40px;"> Merge </h1>

  Now that we have the code for mergesort, you might already have noticed that we still have to write our merge function. The merge function should take two sorted arrays as its argument, and it should return a sorted array that combines all the elements in the two arrays. Here's the strategy we will use: Suppose our two arrays are called a and b. We will set two counters, i and j, and initially, they will point to the start of arrays a and b respectively. Then, we will check: which index points to the greater element? We will pick that element and add it to the start of our new array v, and we will increment the counter (i or j) that pointed to that element. We will keep doing this, until one of our counters has reached the length of our array. In this case, we have added all the elements of one array to our resulting array v. Hence, we are left with the rest of the elements of the second array. Since it is sorted, we can add them sequentially to our resulting array v. Here is the C++ code form merge:

  {% highlight C++ %}
  vector<int> merge(std::vector<int> a, std::vector<int> b) {
    // counter for a
    int i = 0;
    // counter for b
    int j = 0;
    std::vector<int> v;
    // loop through both vectors at the same time, and pick the
    // smallest value of each vector at each iteration. Add this value
    // to a new resulting vector.
    while(i < a.size() && j < b.size()) {
      if(a[i] < b[j]) {
        std::cout << a[i] << '\n';
        v.push_back(a[i]);
        i++;
      } else {
        v.push_back(b[j]);
        j++;
      }
    }
    // When you have looped through all the values of a vector, add
    // the rest of the values of the other vector to the resulting vector.
    while(i < a.size()) {
      v.push_back(a[i]);
      i++;
    }
    while(j < b.size()) {
      v.push_back(b[j]);
      j++;
    }
    return v;
  }
  {% endhighlight %}

  <h1 style="font-size: 40px;"> Runtime Analysis </h1>
  <h1>Using intuition</h1>
  We can derive the run time of merge sort in many ways, but we will first start with the most intuitive one. If we look at the first tree we drew, what is its height? The 0th level is the root, and the levels go up to 3. You might have noticed we have 8 elements in the array. The number of levels in our tree is <script type="math/tex">\log(8)</script>. As a general rule, for an array of <script type="math/tex">n</script> elements, we will have a tree of height <script type="math/tex">\log(n)</script>.
  <br><br>
  Now, for each of these levels, we have to go through every array at this level to perform merge. At level 0, we don't do anything because the array is sorted and we're done. At level 1, we have to merge 2 arrays of length <script type="math/tex">n/2</script> each, which means we have to perform <script type="math/tex">n</script> operations. At level 2, we have to merge 2 sets of 2 arrays each. That means we have to go through 4 arrays of length <script type="math/tex">n/4</script> each. We hence also have to do n operations. I think you can see the trend at this point. Each level of the tree does <script type="math/tex">O(n)</script> work.
  <br><br>
  We've said before that our algorithm has <script type="math/tex">n</script> levels. Hence, the runtime of the algorithm is <script type="math/tex">O(n\log(n))</script>
  <br><br>

  <h1>Using Recurrence Relations</h1>
  We find the runtime using recurrence relations. We would do this the following way.
  <br>
  The work <script type="math/tex">T(n)</script> needed to sort a given array is give by the following reccurence relation:

  $$ T(n) = 2T(\frac{n}{2}) + cn $$
  Instead of <script type="math/tex">cn</script>, we could use <script type="math/tex">O(n)</script>. The sole point is that for an array of size <script type="math/tex">n</script>, we need to apply mergesort to 2 arrays of size <script type="math/tex">\frac{n}{2}</script>, which takes <script type="math/tex">2T(\frac{n}{2})</script> work, and we need to merge them, which takes <script type="math/tex">O(n)</script> work.

  We can solve this recurrence in many ways, one of which is by using the <a href="https://brilliant.org/wiki/master-theorem/" target="_blank"> Master Theorem</a>. Here's a good link that explains it. The biggest part of the work is coming up with the recurrence relation, so I won't go through explaining the master theorem right now (let me know if you'd like me to make a blog post about it). Anyways, when you apply the theorem, you find that the runtime is indeed <script type="math/tex">O(n\log(n))</script>


</div>
