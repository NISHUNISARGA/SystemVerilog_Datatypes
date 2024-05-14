# **SYSTEMVERILOG DATATYPES**
## Sections
- [TYPEDEF](#typedef)
    * [Syntax](#syntax)
    * [Benefits](#benefits)
    * [ClassSyntax](#classsyntax)
- [STRUCT](#struct)
    * [StructSyntax](#structsyntax)
    * [Packed](#packed)
    * [Unpacked](#unpacked)
- [UNION](#union)
## **TYPEDEF**
In SystemVerilog, typedef is used to give a new name to an existing data type. This can be useful when you have a complex data type that is used in multiple places in your code.
---
### Syntax:

            typedef data_type new_name[Range];
---

> NOTE: Few data types have inherent typedef means even though we don't declare them as typedef they will act as user defined datatype. 

    Example: class eth_pkt;
                ...
              endclass
     here class is not a data type but it's a keyword.

     module top;
        eth_pkt pkt;
        ---
    endmodule

    here eth_pkt behaves as user defined datatype. 
    pkt is a variable. 




### **Benefits:**
- <span style="color:orange">Improved readability:</span> By giving a complex data type a descriptive name, the code becomes easier to read and understand. 
        
        Example: instead of using bit [7:0] to declare an 8-bit vector
                 you can use typedef bit [7:0] ubyte;

                now we can use ubyte whereever we wants to declare 8bit bit datatype

                class sample;           |       typedef bit [7:0] ubyte;
                    rand bit [7:0]A;    |       class sample;
                                        |           rand ubyte A;            
                endclass                |       endclass

- <span style="color:orange">Increased maintainability:</span> If you need to change the definition of a complex data type, you only need to modify the typedef statement. Without typedef, you would need to find and modify every instance of the data type in your code.
    
        Example: typedef eth_pkt pktQ[$];
        now pktQ will act as a datatype.
- <span style="color:orange">Reduced errors:</span> By using typedef to define complex data types, you can reduce the risk of errors caused by using the wrong data type.
- <span style="color:orange">Improved code reuse:</span> By defining complex data types with typedef, you can reuse them in multiple modules and packages.
---
### <span style="color:green">Example 1:</span>

    Declare array(size = 10) of dynamic array(size = 5) of queue of integers(3)
    Note: while normal declaring of variable: ->
            if you're using typedef: <-

            according to question, this multidimensional can hold the integer value means the datatype is int

            integer A[9:0][5][$:3];

            using typedef 

            typedef integer Q[$:3];         |           typedef integer Q[9:0][5][$:3];
            typedef Q QDA[5];               |    
            typedef QDA QDA_A[9:0];         |           module top;
                                            |                Q  var;
            module top;                     |                ---
                QDA_A var;                  |                endmodule
                ---
            endmodule

---   

### <span style="color:green">Example 2:</span>         

- A `typedef` is used to provide a forward declaration of the class.
- In some cases, the class needs to be instantiated before the class declaration. In these kinds of situations, the typedef is used to provide a forward declaration of the class.

### ClassSyntax:
    typedef class class_name;       

* consider two classes c1 and c2.
* c2 is instantiated inside c1 and c1 inside c2. 
* Both classes need the handle of each other.
* As execution will happen in sequential order. Dependency between both the classes leads to a compilation error.

`without typedef/forward declaration of class`

    //class-1
        class c1;
            c2 c;    //using class c2 handle before declaring it.
        endclass

    //class-2
        class c2;
            c1 c;
        endclass
 
    module typedef_class;
        initial begin
            c1 class1;
            c2 class2;
            $display("Inside typedef_class");
    end
    endmodule

    OUTPUT :
                ERROR VCP2000 "Syntax error. Unexpected token: c2[_IDENTIFIER]." "testbench.sv" 2  5
                FAILURE "Compile failure 1 Errors 0 Warnings  Analysis time: 0[s]."

>The compilation error of the above example can be avoided by using a typedef.

`with typedef/forward declaration of class`

    typedef class c2;
    //class-1
        class c1;
            c2 c;    //using class c2 handle before declaring it.
        endclass

    //class-2
        class c2;
            c1 c;
        endclass
 
    module typedef_class;
        initial begin
            c1 class1;
            c2 class2;
            $display("Inside typedef_class");
    end
    endmodule

    OUTPUT :
                Inside typedef_class
---            

## **STRUCT**
A struct is a collection of variables of different types into a single entity. 
There are some requirements where you might need to keep track of different data types for large quantities.
In the SPI controller, we have used the address register, data register and control register being declared as one register what if there is a requirement where we have a few more extra registers and need to declare all of them separately?
In this kind of situation, we can use structure.
---
### StructSyntax:
> WITHOUT TYPEDEF

            struct {
                DATA_TYPE1 VAR1;
                DATA_TYPE2 VAR2;
                ...
            }

> WITH TYPEDEF

                UPACKED                 |               PACKED
     ___________________________________|___________________________________      
                                        |     
        typedef struct {                |        typedef struct packed{   
                DATA_TYPE1 VAR1;        |               DATA_TYPE1 VAR1;
                DATA_TYPE2 VAR2;        |               DATA_TYPE2 VAR2;
                ...                     |               ...
        }                               |           }
---

### Packed:
* Packed structures are stored in memory as a contiguous block of bytes, with each member of the structure occupying a fixed number of bits.(The elements are packed tightly in memory without any gaps. This means that the memory layout of a packed structure is similar to a single vector.)
* Packed structures are useful for modeling hardware structures, such as registers or buses, where the data is packed into a fixed-size block.
    
    Example:
        typedef struct packed {
            bit [7:0] byte1;
            bit [7:0] byte2;
            bit [7:0] byte3;
        } packed_struct_t;
        
![alt text](<Screenshot 2024-05-09 092038.png>)

---
### Unpacked:
Unpacked structures are stored in memory as separate variables, with each member of the structure occupying its own memory location. Unpacked structures are useful for modeling software data structures, such as records or objects, where the data is spread out in memory.
    
    Example:
        typedef struct packed {
            bit [7:0] byte1;
            bit [7:0] byte2;
            bit [7:0] byte3;
        } packed_struct_t;

![alt text](<Screenshot 2024-05-09 095217.png>)

> [click here to know more](<http://www.testbench.in/SV_07_STRUCTURES_AND_UNIOUNS.html>)
---
### <span style="color:green">Example 1:</span>

         typedef struct {
            string name;
            int price;
            int quantity;   
        } grocery_item_t;

        module top;
          grocery_item_t grocery_list[5];

            int total_amount;

            initial begin
              grocery_list={{"Apple",2, 10},{"Banana", 1, 20},{"Orange", 3, 15},{"Milk", 5, 5},{"Bread", 4, 8}};
                total_amount = 0;

                // total price calculation
              total_amount = grocery_list.sum() with (item.price*item.quantity);
              $display("%p \n %d",grocery_list,total_amount);

              // sorting based on price
              grocery_list.rsort() with (item.price*item.quantity);
              $display("%p",grocery_list);

              // sorting based on quantity
              grocery_list.rsort() with (item.quantity);
              $display("%p",grocery_list);
            end
        endmodule

> <span style="color:yellow">Note:</span> class also groups the different datatypes variable into multiple entities. Still, class is a dynamic datatype where the structure is a static datatype and in structure, we can't write constraints, or methods(tasks and functions).
 
 * To access the elements of the structure we have to use the '.'(XMR)

        example: 
             grocery_list.name = "APPLE";
             grocery_list.price = 30;
             grocery_list.quantity = 10;
