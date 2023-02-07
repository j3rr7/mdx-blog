# Introduction

Let's try to solve two sum problem in many programming language
i'll try to explain briefly the algorithm behind every solution

# Problem
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.

Example 1:

Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].

Example 2:

Input: nums = [3,2,4], target = 6
Output: [1,2]

Constraints:
- 2 <= nums.length <= 104
- -10^9 <= nums[i] <= 10^9
- -10^9 <= target <= 10^9
- Only one valid answer exists.


## Solution 1

First solution is using brute force method
by using nested loop we could check every 
pair of element in input array and see if 
they add up to the target

here's the code in golang
```go
func twoSum(nums []int, target int) []int {
    for i := 0; i < len(nums); i++ {
        for j := i + 1; j < len(nums); j++ {
            if nums[i]+nums[j] == target {
                return []int{i, j}
            }
        }
    }
    return []int{}
}
```
this approach is using time complexity of O(n^2)
because we are iterating through the array twice
this is not the best solution but it's the easiest one

Let's see how well this solution perform
```go
package main

import "fmt"

func twoSum(nums []int, target int) []int {
	for i := 0; i < len(nums); i++ {
		for j := i + 1; j < len(nums); j++ {
			if nums[i]+nums[j] == target {
				return []int{i, j}
			}
		}
	}
	return []int{}
}

func main() {
	nums := []int{2, 7, 11, 15}
	target := 9
	result := twoSum(nums, target)
	fmt.Println(result) // Output: [0 1]
}
```
Nice, it works
now lets try another case
```go
func main() {
	nums := []int{3, 2, 4}
	target := 6
	result := twoSum(nums, target)
	fmt.Println(result) // Output: [1 2]
}
```

## Solution 2
Second solution lets try to code this first,
for educational purposes lets use ruby language
```ruby
def two_sum(nums, target)
    num_hash = {}
    nums.each_with_index do |num, i|
        complement = target - num
        if num_hash.has_key?(complement)
            return [num_hash[complement], i]
        end
        num_hash[num] = i
    end
    return []
end
```
The two_sum function takes in two parameters: an array of integers, nums, and an integer target. 

It returns an array of two integers from nums such that the sum of these integers is equal to target.

This function first creates an empty hash, num_hash, and then iterates over the elements of nums using each_with_index method. 

For each element, it calculates its complement (i.e. target - num) 

and checks if the complement exists in the num_hash hash.

If it does, it returns an array of the complement's index in num_hash and the current index i.

If not, it adds the current element and its index to the num_hash hash.

Finally, if it completes the loop without finding the target sum, it returns an empty array.