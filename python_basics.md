**global keyword**: 
* Normally, you can read a global variable (say `train_df`) inside a function without any special keywords.
* However, the moment you use an assignment operator (like `train_df = ...`), Python assumes you are creating a new local variable named train_df that only exists inside that function.
* So by using global keyword inside a function, you are explicitly stating: "Don't create a local version; when I reassign these names, update the ones defined at the top level of the script."
* But but.... use of global variables in not encouraged for prod level code. Why?
    * _Race condition may arise in your code_ - Same global variable being modified at same time by two different threads.
    * _Difficulty in testing_ - A test that modifies a global variable changes the environment for every other test that follows
    * _Missing D from SOLID_ - Dependency Injection says, "a function should receive everything it needs to do its job as an argument." 
