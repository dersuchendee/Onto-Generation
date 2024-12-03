# Prompting techniques
## Memoryless CQbyCQ
This is similar to [CQbyCQ](https://github.com/LiUSemWeb/LLMs4OntologyDev-ESWC2024/blob/main/Prompts/CQbyCQ.md) but 1- the {rdf} section, which refers to the previous interactions, is always empty. 2- the {story} does not contain all CQs (actually no CQ), and the current CQ is moved to {CQ} section. This causes a considerable reduction in the LLM's context.

```python
"""Your task is to contribute to creating a piece of well-structured ontology by reading information that appeared in the given story, requirements, and restrictions (if there are any).
The way you approach this is first you pick this competency question "{CQ}" and read the given turtle RDF (we append the code at the end of the previous one) to know what is the current ontology till this stage (it can be empty at the beginning). Then you add or change the RDF so it can answer this competency question. Your output at each stage is an append to the previous ones, just do not repeat. You only need to solve the question number so do not touch the next questions since they belong to the next stages of development. you can read these definisions to understand the concepts:
lasses are the keywords/classes that are going to be node types in the knowledge graph ontology. try to extract all classes, in addition, classes are also can be defined for reification. We use Turtle Syntax for representation. Hierarchies are rdfs:subClassOf in the turtle syntax. They can be used to classify similar classes in one superclass. To do this you can find similar nodes and create/use a class as their parent class, for example, adding the node "Cl_employee" is a good middleware and superclass for "Cl_Professors" and "Cl_Administrator" if the story is about modeling ontology of a university. Mostly the lengthier the hierarchy the better. One way can be categorizing classes into several classes and creating superclasses for them. Important: Class names have Cl_ as the prefix for example Cl_Professors. Also keep in mind you can add Equivalent To, General class axioms, Disjoint with, and Disjoint Union of, for each class.
In your ontology modeling, for each competency question, when faced with complex scenarios that involve more than two entities or a combination of entities and datatypes, apply reification. Specifically, create a pivot class to act as an intermediary for these entities, ensuring the nuanced relationships are accurately captured. For instance, when representing "a user accessed a resource at a given time", establish a pivot class like Cl_UserResourceUsage, linked from the user, resource, and the specific time of access to Cl_UserResourceInteraction, rather than directly connecting the user to both the resource and time.
Then you need to create properties (owl:Property). In this step, you use classes from the previous stage and create object and data properties to connect them and establish the ontology. Always output a turtle syntax, if you need more classes to model a competency question between more than 2 concepts, feel free to add more pivot (reification) classes here. try to find as much relation as possible by reading competency questions, restrictions, and stories. At this stage, you can create both data and object properties. Data properties are between classes or hierarchy classes and data types such as xsd:string, xsd:integer, xsd:decimal, xsd:dateTime, xsd:date, xsd:time, xsd:boolean, xsd:byte, xsd:double, xsd:float and etc. For example, in the university domain, we have: employee_id a owl:Property ; rdfs:domain :cl_teacher ; rdfs:range xsd:integer. Object properties are between classes. try to find as much relation as possible by reading competency questions and the story. Feel free to use rdfs:subPropertyOf for creating hierarchies for relations. For modeling properties (object or data properties) if it is necessary, use these relations characteristics: Functional, Inverse functional, Transitive, Symmetric, Asymmetric, Reflexive, and Irreflexive. Also, you are flexible in domain and range so you can use Cl_class1 or Cl_class2 in domain and range or disjoint with, the inverse of between relations.
It is common to forget to add relations that are related to reification: In RDF reification, achieving precise modeling is pivotal, especially when handling multifaceted scenarios where mere binary associations fall short. Take for instance the statement, "a user used a resource at a time". While it might initially seem to involve a direct link between a 'user' and a 'resource', it inherently embodies three entities: a 'user', a 'resource', and a 'time'. Directly connecting 'user' to both 'resource' and 'time' fails to capture the essence of the statement, as it obscures which resource was utilized by the user at a specific time. To address this, a more sophisticated modeling approach is needed, invoking a pivot class, Cl_usingResource. This pivot class acts as an intermediary, linking both Cl_user and Cl_resource. Furthermore, it integrates a time property to denote the exact instance of usage. By employing this method, we can coherently model the statement, ensuring that the user's interaction with a specific resource at a distinct time is unambiguously represented. This approach highlights the imperative of ontology design patterns and the necessity of intermediary nodes when modeling complex relationships involving multiple entities or a mix of entities and datatypes.
Upon implementation of restrictions, feel free to use owl:equivalentClass [ rdf:type owl:Restriction ;  owl:onProperty :{{relation}} ;  owl:allValuesFrom :{{Class}} ] ; in this way, you can put restrictions for classes such as class Cl_C1 is the only class that uses the relation R. or you can put soft restrictions by using owl:someValuesFrom. Also, you can use general class axioms: [ rdf:type owl:Restriction ; owl:onProperty :R1 ; owl:someValuesFrom :Cl_1 ; rdfs:subClassOf :Cl_2 ] when you want to put restrictions on the definition of a class based on its relation and the definition is necessary but not enough (if it is enough it would be equivalent to owl:equivalentClass).

these are the prifixes:
@prefix : <http://www.example.org/test#> .
@prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
@prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
@prefix xsd: <http://www.w3.org/2001/XMLSchema#> .
@prefix owl: <http://www.w3.org/2002/07/owl#>.

Important: before writing the owl code, write this text: is this competency question answerable by the previous version of the RDF (given down) or not? (most likely (~90%) is it not answerable). If no, write a reification class for this question if needed. then solve it. if it was answerable, simply rewrite the given rdf in the output without changing it.
here is one story:

{story}

End of story
here are some possible mistakes that you might make:
1- forgetting to add prefixes at the beginning of the code.
2- forgetting to write pivot classes at the beginning before starting to code.
3- your output would be concatenated to the previous output rdf, so don't write repetitive words, classes, or ...
4- in your output put all of the previous RDF classes, relations, and restrictions and add yours. your output will be passed to the next stage so don't remove previous code (it is going to replace the previous rdf)
5- you usually forget to write the name of the reification (pivot) that you want to create at the beginning of the output
6- In reification, the reification node (pivot class) is connected to all related classes by object properties, not by the subclassof. it can be a subclass of something, but for reification, it needs object properties.
common mistakes in extracting classes:
1- mistake: not extracting all classes and missing many of them. classes can be found in the story, or in the competency question number and restrictions.
2- Returning empty answer
3- Providing comments or explanations
4- Extracint classes like 'Date', and 'integer' are wrong since they are data properties.
5- not using RDF reification: not extracting pivot classes for modeling relation between classes (more than one class and one data property, or more than two classes)
6- extracting individuals in the text as a class
7- The pivot class is not a sublcass of its components.
common mistakes in the hierarchy extraction:
1- creating an ontology for non-existing classes: creating a new leaf and expanding it into the root
2- returning empty answer or very short
3- Providing comments or explanations
4- Extracting attributes such as date, time, and string names that are related to data properties
5- Forget to add "" around the strings in the tuples
Common mistakes in the object_properties:
1- returning new variables with anything except object_properties
2- returning empty answer or very short
3- providing comments or explanations
4- when the pivot class is created, all of the related classes should point to it (direction of relation is from the classes (domains) 'to'  pivot class (range))
Common mistakes in the data_properties:
1- returning new variables with anything except data_properties
2- returning empty answer or very short
3- providing comments or explanations
Here is the last RDF:
{rdf}"""
```


## Ontogenia
### Description

Ontogenia is a technique for ontology generation using metacognitive prompting in Large Language Models (LLMs). First introduced by Lippolis, A. S., Ceriani, M., Zuppiroli, S., & Nuzzolese, A. G., this method is explained in their poster "Ontogenia: Ontology Generation with Metacognitive Prompting in Large Language Models." This technique refines the metacognitive prompting process to guide an LLM in the role of an ontology engineer, providing it with guidelines for effective ontology formalization.

The inputs for the prompting process include:
- A user story, a new addition not included in previous work.
- One or more Competency Questions (CQs) to model.
- A Metacognitive Prompting Procedure.
- Guidelines for ontology formalization.

Ontogenia is designed to handle one CQ at a time, generating and merging the resulting ontology at each step. The procedure merges five steps from Wang et al.'s metacognitive prompting method with the eXtreme Design methodology, emphasizing collaboration between ontology designers and domain experts, and iterative assessment to ensure the ontology meets initial requirements.

### How It Works with Prompts

#### Single CQ Ontology Design

1. **Procedure Definition:**
   The procedure is defined in a separated file.

2. **Ontology Elements:**
    ```python
    ontology_elements = "Classes, Object Properties, Datatype Properties. Object properties need to have domain and range. All of them need to have an explanation in the rdfs:label. You also need to add restrictions, and subclasses for both classes and object properties when applicable."
    ```

3. **Load Patterns:**
    ```python
    data = pd.read_csv('data/patterns.csv')  # Update the path to your CSV file
    patterns_json = json.dumps({row['Name']: row['Pattern_owl'] for _, row in data.iterrows()})
    ```

4. **Design Ontology Function:**
    ```python
    def design_ontology(patterns_json, CQ, scenario, procedure, ontology_elements):
        prompt = (
            f"Read the following instructions: '{procedure}'. Based on the scenario: '{scenario}', design an ontology module that comprehensively answers the following competency question: '{CQ}'. You can use the following ontology design patterns in OWL format: {patterns_json}. Remember what are the ontology elements: {ontology_elements}. When you're done send me only the whole ontology you've designed in Turtle (.ttl) format, do not comment."
        )
    ```

#### Grouped CQs Ontology Design

1. **Procedure Definition:**
    The procedure is defined in a separated file.

2. **Ontology Elements:**
    ```python
    ontology_elements = "Classes, Object Properties, Datatype Properties. Object properties need to have domain and range. All of them need to have an explanation in the rdfs:label. You also need to add restrictions, and subclasses for both classes and object properties when applicable."
    ```

3. **Load Patterns:**
    ```python
    data = pd.read_csv('data/patterns.csv')  # Update the path to your CSV file
    patterns_json = json.dumps({row['Name']: row['Pattern_owl'] for _, row in data.iterrows()})
    ```

4. **Design Ontology Function with Previous Output:**
    ```python
    def design_ontology(patterns_json, CQ, scenario, procedure, ontology_elements, previous_output=""):
        prompt = (
            f"Following the previous output: '{previous_output}' Read the following instructions: '{procedure}'. Based on the scenario: '{scenario}', design an ontology module that comprehensively answers the following competency question: '{CQ}'. You can use the following ontology design patterns in OWL format: {patterns_json}. Remember what are the ontology elements: {ontology_elements}. When you're done send me only the whole ontology you've designed in Turtle (.ttl) format, do not comment."
        )
    ```

### Detailed Procedure for Ontology Design

1. **Analyze the Competency Question:**
    - Understand the CQ to identify key components.

2. **Identify the Context:**
    - Define the scope and domain of the ontology.

3. **Decompose the Competency Question:**
    - Break down the CQ into subject, predicate, object, and predicate nominative.
    - Map these elements to ontological constructs.

4. **Determine Subclass Relationships:**
    - Establish subclass relationships using rdfs:subClassOf.

5. **Extend the Ontology with Restrictions:**
    - Apply restrictions like owl:allValuesFrom, owl:hasValue, and owl:someValuesFrom.
    - Define cardinality with owl:minCardinality.

6. **Define Equivalent and Disjoint Classes:**
    - Use owl:equivalentClass and owl:disjointWith for class relationships.

7. **Integrate and Refine:**
    - Review interrelationships for logical consistency and completeness.

8. **Validate and Explain:**
    - Confirm the ontology answers all CQs and explain the reasoning behind each element.

9. **Evaluate Confidence and Test:**
    - Test the ontology with instances and assess confidence based on performance.
