A **Bloom filter** is a space-efficient probabilistic data structure used to test whether an element is a member of a set. It is very fast and consumes minimal memory, making it ideal for applications where quick membership checks are needed. However, it comes with a trade-off: false positives are possible, but false negatives are not.

It may return false positives (says present but actually not), but it never returns false negatives (if it says not present, it is guaranteed correct).

---

### How does it work?

To build a bloom filter we need an array of bits and a couple fast hash functions.

* Say we have an empty Bloom filter with 10 bits: `[0, 0, 0, 0, 0, 0, 0, 0, 0, 0]`.
* Suppose we want to insert the element "apple", and we have 3 hash functions that hash "apple" to positions 1, 4, and 7 in the array.
* We set those bits to 1: `[0, 1, 0, 1, 0, 0, 1, 0, 0, 0]`.
* Now, if we check for "apple", we hash it again and check bits 1, 4, and 7. Since they are all 1, the Bloom filter says "apple" is probably in the set.
* If we query for "orange", and any of the corresponding bits (based on its hash) are 0, we know it's definitely not in the set.

    ![Bloom Filter Diagram](https://media2.dev.to/dynamic/image/width=800%2Cheight=%2Cfit=scale-down%2Cgravity=auto%2Cformat=auto/https%3A%2F%2Fdev-to-uploads.s3.amazonaws.com%2Fuploads%2Farticles%2Fesf6ijt4qzdgakdg43ht.gif)

---

### Use cases of Bloom Filters

* **In Content Delivery Networks (CDNs):** Bloom filters are widely used in Content Delivery Networks (CDNs) to optimize efficiency, especially when handling large amounts of data. Their primary role in a CDN is to help in quickly determining whether a content or a resource is cached at a particular edge server or not, without requiring an exhaustive search.
* **Financial fraud detection (finance):** This application answers the question, "Has the user paid from this location before?", thus checking for suspicious activity in their users' shopping habits.
* **Ad placement (retail, advertising):** This application answers these questions:
  * Has the user already seen this ad?
  * Has the user already bought this product?
* **Check if a username is taken (SaaS, content publishing platforms):** This application answers this question: Has this username/email/domain name/slug already been used?

---

### Key reasons Bloom filters are used:

* **Speed:** Queries (to check for membership) are performed in constant time, $O(k)$, where $k$ is the number of hash functions used. Space Complexity
* **Space Complexity:** Space complexity is $O(m)$, where $m$ is the size of the bit array.
* **Space Efficiency:** Bloom filters are highly space-efficient compared to other data structures like hash tables or arrays when it comes to storing large sets.