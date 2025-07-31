---
title: "Todo List"
tags: ["Solidity", "App"]
---

# Tasks

## 1

Declare a public state variable named todos which will store an array of Todo structs.

## 2

Write a function which will create a new Todo with text set from input and then stores the new Todo into the array todos.

## 3

Write a function named updateText(uint256 index, string calldata text) which will update the text of Todo stored at index to the new value text.

## 4

Write a function to toggle completed of a Todo stored in todos at a given index.

The function toggleCompleted(uint256 index) will toggle completed of Todo stored in todos at index.

For example, if completed is true, this function should flip it to false.

Likewise if false, set it to true.

## 5

Write function get(uint256 index) which will return the Todo stored at index.

This function should return two outputs, text and completed.

---

# Solution

```sol
// SPDX-License-Identifier: MIT
pragma solidity 0.8.26;

contract TodoList {
    struct Todo {
        string text;
        bool completed;
    }
    
    Todo[] public todos;
    
    function create(string calldata text) external {
        Todo memory todo = Todo(text, false);
        todos.push(todo);
    }
    
    function updateText(uint256 index, string calldata text) external {
        todos[index].text = text;
    }
    
    function toggleCompleted(uint256 index) external {
        todos[index].completed = todos[index].completed == false ? true : false;
    }
    
    function get(uint256 index) external view returns (string memory, bool) {
        return (todos[index].text, todos[index].completed);
    }
}
```
