### **Day 52: Randomization in SystemVerilog**

---

### **Objective:**

Learn how SystemVerilog supports **randomization** of variables, fields, and objects to generate diverse and constrained stimulus for verification.

---

## ✅ 1. What is Randomization?

Randomization allows you to automatically generate **random values** for variables in a controlled way, using:

* Built-in `randomize()` method
* User-defined constraints

It is a powerful feature for **constrained-random verification**, where we want **unpredictable but valid** test scenarios.

---

## ✅ 2. Types of Randomization

| Type         | Description                                     |
| ------------ | ----------------------------------------------- |
| **Implicit** | Randomizing variables using `rand`              |
| **Explicit** | Calling `.randomize()` with/without constraints |

---

## ✅ 3. Declaring Random Variables

```systemverilog
class Packet;
    rand bit [7:0] data;
    rand bit [1:0] priority;
endclass
```

---

## ✅ 4. Basic Randomization

```systemverilog
Packet p = new();
p.randomize(); // fills data and priority with random values
```

---

## ✅ 5. Adding Constraints

You can restrict the values that `rand` variables can take.

```systemverilog
class Packet;
    rand bit [7:0] data;
    rand bit [1:0] priority;

    // Constraint block
    constraint valid_data {
        data > 10 && data < 200;
    }

    constraint priority_valid {
        priority != 2'b11; // Disallow critical
    }
endclass
```

---

## ✅ 6. In-line Constraints

```systemverilog
p.randomize() with { data % 2 == 0; }; // even data only
```

---

## ✅ 7. Controlling Randomization

* **`rand`**: normal random variable
* **`randc`**: cyclic random variable (no repeats until all values used)

```systemverilog
randc bit [1:0] priority; // will generate 0,1,2,3 in a shuffled order
```

---

## ✅ 8. Example: Randomized Packet Class

```systemverilog
class Packet;
    rand bit [7:0] address;
    rand bit [3:0] payload;
    rand bit is_write;

    constraint addr_range {
        address inside {[100:120]};
    }

    constraint payload_cond {
        if (is_write) payload > 5;
    }

    function void display();
        $display("Addr: %0d, Payload: %0d, Write: %0b", address, payload, is_write);
    endfunction
endclass

module tb;
    Packet pkt = new();

    initial begin
        repeat (5) begin
            assert(pkt.randomize());
            pkt.display();
        end
    end
endmodule
```

---

## ✅ Output (Sample):

```
Addr: 103, Payload: 9, Write: 1
Addr: 117, Payload: 2, Write: 0
Addr: 112, Payload: 7, Write: 1
...
```

---

## ✅ Summary

| Feature             | Purpose                               |
| ------------------- | ------------------------------------- |
| `rand` / `randc`    | Declare random variables              |
| `randomize()`       | Generate random values                |
| Constraints         | Control and limit randomized values   |
| In-line Constraints | Add temporary restrictions at runtime |


