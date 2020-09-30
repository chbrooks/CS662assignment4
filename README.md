
## Propositional Reasoning, Trees and Rules

#### Due: October 18, 11:59pm

#### 70 points

##### Part 1:   Propositional Logic sentences

Please include a document in your repo answering these questions.

**(5 points)** Encode the following sentences in propositional logic, using only the
following terms:

- H: Homer is happy. 
- B: Bart is happy. 
- M: Marge is happy. 
- Sun: The sun is shining 
- S: There is school. 
- L: Lisa is happy.


- Homer is happy if and only if Bart is happy and Marge is happy. 
- If there is no school and the sun is shining, then Bart is happy.
- If there is school, then Lisa is happy. 
- If Lisa is not happy, then Marge is happy. 
- The sun is shining. 
- Lisa is not happy.
 
**(5 points)** Convert each of the sentences above to CNF.

**(5 points)** Use  **resolution with refutation** to show that Homer is happy.


##### Part 2: Learning decision trees
In this assignment, you'll implement three different algorithms
for learning rules. The first learns a decision tree, the second a
decision list, and the third a ruleset. 

For each algorithm, you will classify instances from several different
datasets. The amount of code you need to write is not huge, but there
are some mental hurdles you'll need to cross to completely understand what's going on.

I have provided some skeleton code to get you started and guide you
through the implementation. You are welcome to make any changes that
you like, but please think carefully before doing so; this is designed
to help make these algorithms easy to implement.


Datasets:

There are two types of datasets included with the assignment.

  - Toy datasets:
    - the tennis dataset
    - the restaurant dataset.
    
    These are both useful for testing your code; they're small, and
    you know what the correct answers are.
    
  - "real" datasets.
  
      - Breast Cancer data. We used this dataset in assignment 1. This dataset contains medical records
      from a large number of women who have had breast cancer. Based
      on their characteristics, we would like to predict whether they will have a recurrence event. 
      - Lymph node data. This dataset contains the medical records for the
      examinations of a large number of patients' lymph nodes. Based
      on these records, we would like to predict their medical
      condition. (Normal, metastaized cancer, malign nodes only, or
      fibrosis in the node.) 
      - Nursery school data. This data set contains nursery school
      applications for a large number of parents. Based on
      characteristics about the parents, we would like to predict
      whether the child should be admitted to nursery school.
      
      These datasets will be much more interesting for evaluating the
      performance of your learning algorithms. All the datasets have a few convenient properties: they are already discretized, they do not contain noise, 
      and there are no missing values. This means that it should be possible for any of these algorithms to completely fit the training data.

   These files are in a format known as [ARFF](https://datahub.io/blog/attribute-relation-file-format-arff). 
   An ARFF file consists of three sections. The first is the comments, which begin with a '%'. The second is the @relation section, which 
   describes each of the variables and the values they can take on. The third is the data itself, with each row representing one instance.
   I've provided you with a file called readARFF.py. to process this. 
   readARFF contains three functions:
   - readArff, which takes as input a filehandle and returns two items:
     - an attribute dictionary which makes an attribute name to a tuple indicating the column that attribute represents in the dataset and a list of the possible values it can take on.)
     - a list of lists containing the data itself. 
     
  - getAttrList. This takes as input the attribute dictionary and returns a list of the attribute names in the same order as the columns they represent.
  - ZeroR. This is a straw-man learning algorithm. It simply returns the most likely classification in a dataset. You may find it useful as a comparator, or in cases where you are unable to split your dataset.
  
A hint: list comprehensions are very helpful for this
   assignment. Often, you'll need to pull out one or more columns from
   the data. So, for example, to get a list containing only the third
   column in a dataset where the last element is equal to some item
   'x', you could do: 
   <pre>
third = [d[2] for d in data if d[-1] == 'x']
</pre>

First, we'll implement the basic decision tree algorithm. 

- **(5 points)** The decision tree is easiest to build in a bottom-up fashion. To
   begin, we'll need a method to compute entropy. it should take 
   as input a list of attribute values, such as ['weak', 'strong',
   'weak', 'weak'] and return a float indicating the entropy in this
   data. I've provided a function stub for you. 

- Next, we'll want to compute remainder. This will tell us, for a
   given attribute, how much information will remain if we choose to
   split on this attribute. I've written this one for you. 


- **(5 points)** Once we know how to compute remainders, we need to be able to
   select an attribute. To do this, we just compute the remainder for
   each attribute and choose the one with the smallest
   remainder. (this will maximize information gain.) The function
   selectAttribute should take as input a list of lists, with each
   list being an instance. I've provided a stub for you.

Now we're now ready to think about building a tree. A tree is a
   recursive data structure which consists of a parent node that has
   links to child nodes. I've provided a TreeNode class for you that
   does this. (you don't need a separate Tree class.)

The TreeNode has the following data members:
- attribute: for non-leaf nodes, this will indicate which attribute
   this node tests. For leaves, it is empty. 
- value. For leaf nodes, this indicates the classification at
   this leaf. For non-leaf nodes, it is empty. 
- children. This is a dictionary that maps values of the
   attribute being tested at this node to the appropriate child, which
   is also a TreeNode. 
 
It also has methods to print itself and to test whether it is a
   leaf.
 
**(10 points)** So we need a method that can build a tree. We will call this
   makeTree. It should take as input a dataset, a list of attribute
   names, the attribute dictionary, and a default value. It should work as follows:
 - If the dataset contains zero entropy, we are done. Create a
   leaf node with value equal to the data's classification and return
   it. 
 - If the dataset is empty, we have no data for this attribute
   value. Create a leaf node with the value set to the default value
   and return it. 
 - Otherwise, we have a non-leaf node. Use selectAttribute to
   find the attribute that maximizes gain. Then, remove that column
   for the dataset and the list of attributes and, for each value of
   that attribute, call makeTree with the appropriate subset of the
   data and add the TreeNode that is returned to the children, then
   return the TreeNode.
   
  - **(5 points)** Now we know how to build a tree. We need to use it, though. To
   do this, you should implement the classify() method in
   TreeNode. classify should take as input the data instance to be
   classified and our attribute dictionary.
   
    This method is also recursive. If we are at a leaf, return the
   value of that leaf. Otherwise, check which attribute this node
   tests and follow the appropriate child. If there is no child for
   this value, return a default value.
   
Congratulations! You now have a working decision tree. Test it
   out on the toy datasets. You might find it helpful to build a
   better printTree method, although this is not required. You might
   also want to add code to pickle your tree to a file, and a main to
   allow you to easily specify options. 

##### Part 3: Decision Lists and Rulesets

An alternative approach to learning rules is to learn either a
decision list or a ruleset. As with the
decision tree, I would recommend starting with the restaurant and
tennis data and then working with a "real" dataset once you are
confident your code works correctly.

We will focus only on datasets that are noise-free. This means that
you should be able to learn rules that cover all of the training set,
and can proceed until either entropy=0 or precision=1. You don't need
to worry about significance.

I've provided you with some code to get you started. It can be found in rule-learning.py, and includes a rule class, plus code for matching left-hand and right-hand sides.
You are welcome to change or modify it however you like. As above, think carefully before making too many modifications, as this structure is intended to help you figure this out.

- **(10 points)** Decision Lists. To begin, you should implement the
  decision list algorithm covered in class. This algorithm uses
  entropy to repeatedly grow rules to cover all instances in a data
  set. It should return an ordered list of rules. You should also
  write a function DTclassify that takes as input a decision list, a
  list of attribute names, and an instance and returns a
  classification. As with the decision tree, I would recommend working bottom-up: first write selectAttribute, then use this to write learnOneRuleDL, then use that to write buildDecisionList. Once that's working, do DLClassify.

-  **(10 points)** Rulesets. Next, you should implement the unordered
  version of this algorithm. It should return an unordered set of
  rules. (these can be stored in a list.) Recall that it does this by
  learning separate rules for each class, using precision to grow
  rules from general to specific. You should write a function
  rulesetClassify that takes as input a ruleset, a list of attribute
  names, and a classification. As with the decision list, I recommend going bottom-up: selectAttributeWithPrecision, learnOneRule, and buildRuleset.


- **(10 points)** Prepare a short document that compares the
performance of your three learners (decision tree, decision list,
ruleset) on any of the "real" datasets. You should use 5-fold cross-validation to measure:

  - Precision 
  - Accuracy 
  - F1
  - Average Number of tests needed to classify an example. 

  If you would like to use sklearn to manage the cross-validation, you may. You can also use your code from assignment 1 if you like.
 
  
