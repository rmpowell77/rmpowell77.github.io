---
layout: post
title: Debugging Custom Implementations of Ranges with Chat-GPT
---

I've been looking into building `std::ranges::enumerate` by hand.  I've been using [Chat GPT](https://chat.openai.com) for assistance with this, and the results are impressive, but also a great learning experience.  Let me illustrate.

I started with the query:

How would I write ranges::enumerate in C++20?

Here's basically the answer I got from Chat-GPT:

```cpp
#include <iostream>
#include <vector>
#include <ranges>

template <std::ranges::input_range R>
auto enumerate(R&& range) {
    using std::begin, std::end;
    using iterator_t = decltype(begin(range));
    using index_t = std::make_signed_t<std::ranges::range_difference_t<R>>;

    struct iterator {
        iterator_t iter;
        index_t index;

        decltype(auto) operator*() const {
            return std::pair{index, *iter};
        }

        iterator& operator++() {
            ++iter;
            ++index;
            return *this;
        }

        bool operator!=(const iterator& other) const {
            return iter != other.iter;
        }
    };

    struct sentinel {
        iterator_t end;

        bool operator!=(const iterator& iter) const {
            return iter.iter != end;
        }
    };

    return std::ranges::subrange(iterator{begin(range), 0}, sentinel{end(range)});
}

int main() {
    std::vector<std::string> words = {"apple", "banana", "cherry"};

    for (auto [index, word] : enumerate(words)) {
        std::cout << "Index: " << index << ", Value: " << word << std::endl;
    }

    return 0;
}
```

That's a pretty impressive answer, but it's not right.  Looking at the definition of [`subrange`](https://en.cppreference.com/w/cpp/ranges/subrange):

```cpp
template<
    std::input_or_output_iterator I,
    std::sentinel_for<I> S = I,
    ranges::subrange_kind K = std::sized_sentinel_for<S, I> ?
        ranges::subrange_kind::sized : ranges::subrange_kind::unsized
>
    requires (K == ranges::subrange_kind::sized || !std::sized_sentinel_for<S, I>)
class subrange : public ranges::view_interface<subrange<I, S, K>>
```

We see one of the requirements of `I` (or `iterator` in our case), is to be a `std::sentinel_for<I>`.  Looking at the definition of the concept [`std::sentinel_for`](https://en.cppreference.com/w/cpp/iterator/sentinel_for), we see:

```cpp
template< class S, class I >
    concept sentinel_for =
        std::semiregular<S> &&
        std::input_or_output_iterator<I> &&
        __WeaklyEqualityComparableWith<S, I>;
```

So that shows that we need to make sure that `iterator` and `sentinel` are weakly comparible, which they aren't -- no comparison overloads have been added to those classes.

What's impressive is that when I point that out to Chat-GPT, it corrects it, giving this new answer:

```cpp
#include <iostream>
#include <vector>
#include <ranges>

template <std::ranges::input_range R>
auto enumerate(R&& range) {
    using std::begin, std::end;
    using iterator_t = decltype(begin(range));
    using index_t = std::make_signed_t<std::ranges::range_difference_t<R>>;

    struct iterator {
        iterator_t iter;
        index_t index;

        decltype(auto) operator*() const {
            return std::pair{index, *iter};
        }

        iterator& operator++() {
            ++iter;
            ++index;
            return *this;
        }

        bool operator!=(const iterator& other) const {
            return iter != other.iter;
        }
    };

    return std::ranges::subrange(iterator{begin(range), 0}, iterator{end(range), 0});
}

int main() {
    std::vector<std::string> words = {"apple", "banana", "cherry"};

    for (auto [index, word] : enumerate(words)) {
        std::cout << "Index: " << index << ", Value: " << word << std::endl;
    }

    return 0;
}
```

Wow.  But putting this into [godbolt](https://godbolt.org/z/rcTrK1o7v) fails to compile.  And this is where we can use a couple of debugging techniques to show why.

The code fails to compile with: `error: no viable constructor or deduction guide for deduction of template arguments of 'subrange'`.  That's probably because it cannot deduce the types of `subrange`, likely because the arguments supplied do not conform to the requirements.  We can check that easily.  Recall the definition of `subrange` was:

```cpp
template<
    std::input_or_output_iterator I,
    std::sentinel_for<I> S = I,
    ranges::subrange_kind K = std::sized_sentinel_for<S, I> ?
        ranges::subrange_kind::sized : ranges::subrange_kind::unsized
>
    requires (K == ranges::subrange_kind::sized || !std::sized_sentinel_for<S, I>)
class subrange : public ranges::view_interface<subrange<I, S, K>>
```

So let's add a quick static_assert check in the code to see if our `iterator` class conforms to those requirements.  I add:

```
    static_assert(std::input_or_output_iterator<iterator>);
```

And that fails.  Interesting.  Well, looking at the definition for [`std::input_or_output_iterator`](https://en.cppreference.com/w/cpp/iterator/input_or_output_iterator), we not that we need to have a `difference_type` and both post-fix and pre-fix operators.  And those are missing in the Chat-GPT version.  So if we add those:

```cpp
    struct iterator {
        iterator_t iter;
        index_t index;
        using difference_type = index_t;

        decltype(auto) operator*() const {
            return std::pair{index, *iter};
        }

        iterator& operator++() {
            ++iter;
            ++index;
            return *this;
        }

        iterator operator++(int) {
            auto old = *this;
            ++iter;
            ++index;
            return old;
        }

        bool operator!=(const iterator& other) const {
            return iter != other.iter;
        }
    };
```

well, we still [fail](https://godbolt.org/z/z18o9zh6r).  But look at the definition for `std::sentinel_for`:

```cpp
template< class S, class I >
    concept sentinel_for =
        std::semiregular<S> &&
        std::input_or_output_iterator<I> &&
        __WeaklyEqualityComparableWith<S, I>;
```

It needs to be Comparable.  And while `iterator` has a `!=` comparision operator, it doesn't have the `==` comparision.  So adding that comparision and we get the full working [solution](https://godbolt.org/z/7931GoGEc):

```
#include <iostream>
#include <vector>
#include <ranges>

template <std::ranges::input_range R>
auto enumerate(R&& range) {
    using std::begin, std::end;
    using iterator_t = decltype(begin(range));
    using index_t = std::make_signed_t<std::ranges::range_difference_t<R>>;

    struct iterator {
        iterator_t iter;
        index_t index;
        using difference_type = index_t;

        decltype(auto) operator*() const {
            return std::pair{index, *iter};
        }

        iterator& operator++() {
            ++iter;
            ++index;
            return *this;
        }

        iterator operator++(int) {
            auto old = *this;
            ++iter;
            ++index;
            return old;
        }

        bool operator==(const iterator& other) const {
            return iter == other.iter;
        }

        bool operator!=(const iterator& other) const {
            return iter != other.iter;
        }
    };

    static_assert(std::input_or_output_iterator<iterator>);
    return std::ranges::subrange(iterator{begin(range), 0}, iterator{end(range), 0});
}

int main() {
    std::vector<std::string> words = {"apple", "banana", "cherry"};

    for (auto [index, word] : enumerate(words)) {
        std::cout << "Index: " << index << ", Value: " << word << std::endl;
    }

    return 0;
}
```

