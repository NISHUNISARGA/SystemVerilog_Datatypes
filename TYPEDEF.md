# <span style="color:yellow">**TYPEDEF**</span>
In SystemVerilog, typedef gives a new name to an existing data type. This can be useful when a complex data type is used in multiple places in your code.
---
## <span style="color:pink">Syntax:</span>

            typedef data_type new_name[Range];
---

> NOTE: Few data types have inherent typedef means even though we don't declare them as typedef they will act as user-defined datatype. 

    Example: class eth_pkt;
                ...
              endclass
     here class is not a data type but it's a keyword.

     module top;
        eth_pkt pkt;
        ---
    endmodule

    here eth_pkt behaves as user user-defined datatype. 
    pkt is a variable. 




## <span style="color:green">Benefits:</span>
- <span style="color:orange">Improved readability:</span> By giving a complex data type a descriptive name, the code becomes easier to read and understand. 
        
        Example: instead of using bit [7:0] to declare an 8-bit vector
                 you can use typedef bit [7:0] ubyte;

                now we can use ubyte wherever we wants to declare 8bit bit datatype

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
    note: while normal declaring of variable: ->
            if you are using typedef: <-

            according to question, this multidimensional array can hold the integer value means the datatype is int

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

## <span style="color:pink">ClassSyntax:</span>
    typedef class class_name;


### <span style="color:green">Example 1:</span>         

    * consider two classes c1 and c2.
    * c2 is instantiated inside c1 and c1 inside c2. 
    * Both classes need the handle of each other.
    * As execution will happen in sequential order. Dependency between both classes leads to a compilation error.

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
                FAILURE "Compile failure 1 Error 0 Warnings  Analysis time: 0[s]."

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
