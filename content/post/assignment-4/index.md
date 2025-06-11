---
title: Assignment 4 - Developing an Application with the Aid of LLM
description: This assignment involves the design and development of a software application aligned with your academic or research focus. Large language models (LLMs) are to be leveraged throughout the development process to enhance productivity and problem-solving. The final software must be compiled into an executable, demonstrating practical application. 
date: 2025-06-11 00:00:00+0000
image: cover4.jpg
tags: 
    - LLM
    - Problem-solving
categories:
    - Assignment
---

# Background: My research topic
Despite a significant decline in aviation fatal accident rates since the 1960s, recent fluctuations—such as the increase in total fatal accidents in 2024—highlight that aviation safety remains a critical concern. Effective risk management plays a foundational role in safety and relies on the timely and accurate identification of hazards. 

![Chart showing a general downward trend in fatal aviation accidents from 1990 to 2023. Data source: Airbus](images/Yearly_fatal_total.png)

<small> Figure1: Yearly total fatal accidents showing a general downward trend in fatal aviation accidents from 1990 to 2023. Data source:[AIRBUS](https://accidentstats.airbus.com/fatal-accidents/) </small>

Accident and incident reports, such as those maintained by the National Transportation Safety Board ([NTSB](https://carol.ntsb.gov/)) and the Aviation Safety Reporting System ([ASRS](https://akama.arc.nasa.gov/ASRSDBOnline/QueryWizard_Filter.aspx)), serve as essential sources of information. These repositories contain rich qualitative narratives describing events and contributing factors. However, the unstructured nature of this textual data poses a major challenge for automated processing, limiting the scalability and consistency of insights derived from it.

## Thesis Problem Statement
The aviation industry faces challenges in quickly analyzing and responding to incidents despite safety advancements. 

Probabilistic risk assessment (PRA) is essential for hazard identification, but its automated large-scale implementation is deprived by the difficulty of processing unstructured data in accident reports. 

Current methods often fail to identify causal relationships from text, leading to incomplete, inaccurate, or overly simplistic risk models that are unable to reflect the complexity of aviation accidents.

## Research Objectives
- Enhance keyword and feature extraction techniques from accident reports.
- Improve the identification of causal relationships between contributing factors.
- Construct causal models to gain a deeper understanding of accident mechanisms.
- Implement probabilistic reasoning through Bayesian networks.

## Methodology
1. **Data Collection:** Unstructured narrative reports from the NTSB and ASRS databases are used as the primary input.

2. **Text Preprocessing:** This includes steps such as tokenization, lowercasing, punctuation removal, acronym expansion, and keyword detection.

3. **Topic Modeling:** Techniques such as Latent Dirichlet Allocation (LDA) are applied to identify recurring themes and contextual clusters within the corpus.

4. **Causal Relationship Extraction:** Rule based or deep learning Techniques.

5. **Graph Construction:** Extracted causal pairs are assembled into a Directed Acyclic Graph (DAG), representing the directional relationships between contributing factors.

6. **Bayesian Network Modeling:** The DAG is then adapted into a probabilistic structure to perform reasoning under uncertainty and support safety decision-making.

7. **Validation and Optimization:** The resulting models are evaluated for accuracy, completeness, and practical relevance to aviation safety experts.

8. Present support decision-making in aviation safety through enhanced risk analysis and hazard identification.

# LLM-Based Application Modules 
Large Language Models (LLMs) serve as the core reasoning components in two key tasks: acronym expansion and causal relation extraction. These tasks are detailed below.

## Acronym Handling
Aviation narratives are often filled with domain-specific acronyms (e.g., “ATC,” “VMC”) whose meanings vary depending on context. Our approach first identifies acronyms enclosed in parentheses using regular expressions and then applies heuristics to infer their likely full forms based on nearby capitalized words and initial-letter patterns.
However, a single acronym may have multiple valid expansions. For instance, "VMC" can refer to Visual Meteorological Conditions or Velocity Made Good depending on the operational context. To resolve this ambiguity, a local Large Language Model (LLM) is leverage, such as Mistral, for disambiguation.

### Implementation

The system constructs a targeted prompt that presents the LLM with:

- the surrounding sentence (context),

- the ambiguous acronym (token), and

- a list of possible expansions (candidates).

The LLM evaluates which candidate best fits the context and returns the most appropriate full form. This disambiguation step ensures that acronyms are expanded meaningfully and precisely, reducing the risk of incorrect data interpretation in downstream tasks such as causal relationship extraction or topic modeling.

The updated acronym map is continuously enriched by storing newly discovered expansions, enhancing the system's adaptability and performance over time. This hybrid design—combining heuristic rules with LLM reasoning—enables accurate, context-sensitive acronym expansion at scale, which is essential for processing large volumes of aviation safety reports.

```csharp
[Input Text] 
   ↓
[Regex Acronym Detection]
   ↓
[Heuristic Candidate Generation]
   ↓
[LLM Disambiguation]
   ↓
[Store in Acronym Map]

```
**Prompt Design**
```python
prompt = (
        f"You are given a sentence and a token. The token is ambiguous and could mean more than one thing.\n"
        f"Your job is to choose the correct meaning based on the context.\n\n"
        f"Context sentence: \"{context}\"\n"
        f"Token: {token}\n"
        f"Choices: {', '.join(candidates)}\n\n"
        f"Which meaning fits best in the sentence? Reply with ONLY the best choice, no explanation."
    )
```

### Performance evaluation
To evaluate the accuracy of acronym extraction and disambiguation in aviation safety texts, we conducted a series of controlled test cases. 

1. Acronym Extraction Evaluation

A total of 25 sentences were curated, each containing one aviation-specific acronym alongside its correct expansion. These were used to test the system’s ability to detect and map acronyms in structured narrative formats, such as those commonly found in aviation safety reports.

Example Test Case:

```python
# Test cases for acronym extraction evaluation
test_cases = [
    {
        "text": "The Aircraft Communications Addressing and Reporting System (ACARS) provides real-time communication between pilots and ground stations.",
        "expected": {"ACARS": "Aircraft Communications Addressing and Reporting System"}
    },
    {
        "text": "The Airworthiness Directive (AD) requires immediate action on all Boeing 737 models.",
        "expected": {"AD": "Airworthiness Directive"}
    },
    {
        "text": "The Emergency Response Plan (ERP) was activated following the runway incursion.",
        "expected": {"ERP": "Emergency Response Plan"}
    },
    {
        "text": "Sensitive components are protected from Electrostatic Discharge (ESD) during assembly.",
        "expected": {"ESD": "Electrostatic Discharge"}
    },
    {
        "text": "The Estimated Time of Arrival (ETA) for the flight is 14:35 local time.",
        "expected": {"ETA": "Estimated Time of Arrival"}
    },

]
```
After running the full set of test cases, the system achieved the following performance metrics:
- Precision: 0.85
    - indicates that the majority of the acronyms identified by the system were correctly matched with their true expansions.
- Recall:    0.77
    - shows that some acronyms present in the text were missed or improperly mapped.
- F1 Score:  0.81
    - reflects a balanced measure of overall performance.

2. Acronym Disambiguation Evaluation

The next stage tested the system’s ability to disambiguate acronyms that have more than one potential meaning, depending on context. This was achieved by:

- Providing a setup sentence in which the acronym is explicitly defined (e.g., “The Acceptable Means of Compliance (ACM) was published.”).

- Following this with a target sentence that reuses the acronym without expansion.

- Asking the system to infer the correct expansion using a local Large Language Model (LLM) and a set of candidate meanings.

Example Test Case:

```python
# Test cases for acronym extraction evaluation
disambiguation_tests = [
    {
        "setup": "The Acceptable Means of Compliance (ACM) was published.",
        "text": "Pilots must comply with ACM before flight.",  # should expand to Acceptable Means of Compliance
        "expected": "Acceptable Means of Compliance"
    },
    {
        "setup": "The Airspace Control Measures (ACM) were defined by the military.",
        "text": "ACM were enforced during the exercise.",  # should expand to Airspace Control Measures
        "expected": "Airspace Control Measures"
    },
    {
        "setup": "The Emergency Response Plan (ERP) was activated.",
        "text": "After the crash, ERP was immediately launched.",  # should expand to Emergency Response Plan
        "expected": "Emergency Response Plan"
    },
    {
        "setup": "The Estimated Time of Arrival (ETA) was confirmed.",
        "text": "The ETA for Flight 8701 is now 15:45.",  # should expand to Estimated Time of Arrival
        "expected": "Estimated Time of Arrival"
    },
]
```

**Summary of Results:**

Out of 10 disambiguation test cases:

✅ 9 acronyms were correctly disambiguated.

❌ 1 acronyms were incorrectly expanded or left unresolved.

**Examples of correct matches include:**

- “ACM were enforced during the exercise.” → Airspace Control Measures

- “ETA for Flight 8701 is now 15:45.” → Estimated Time of Arrival

- “FBI agents arrived on the scene.” → Federal Bureau of Investigation

**Failures included:**

- “Technicians wore grounding straps to prevent ESD.” → Expected: Electrostatic Discharge, but the expansion was not correctly substituted.

These results indicate strong performance by the disambiguation module, particularly in cases where the context provides clear semantic clues. However, further refinement is needed to handle subtle or technical uses where context is sparse or ambiguous.


## Causal Relation Extraction and DAG Construction 

To automatically identify causal relationships between safety-related concepts described in narrative reports and represent them as a structured causal graph. The goal is to process text (e.g., accident reports, news, academic papers) and build a graph of “X causes Y” relationships only if those concepts are in a predefined list of keywords.

### Implementation
The core idea is to process individual sentences, detect whether multiple relevant keywords co-occur, and extract cause-effect relations between them using a local LLM.

**Keyword Filtering**
Each sentence is scanned to identify the presence of predefined domain-specific keywords (e.g., “weather,” “runway,” “delay,” “crew fatigue”).

**Prompt Design**
If two or more keywords appear, the sentence is submitted to the LLM with a strict instruction prompt such as:

```python
"""Analyze this sentence to identify ALL DIRECT causal relationships between these keywords: {', '.join(sent_keywords)}.

Follow these rules ABSOLUTELY:
1. Extract EVERY instance where Keyword X directly causes/influences Keyword Y
2. For chain relationships (A causes B causes C), extract BOTH A->B AND B->C
3. For multiple causes (A because B and C), extract BOTH B->A AND C->A
4. For multiple effects (B and C because A), extract BOTH A->B AND A->C
5. Output EXACTLY one "KeywordX -> KeywordY" per line, no additional text
6. Use ONLY exact keyword matches from the provided list
7. PRESERVE NEGATIONS (e.g., "not A causes B" yields A->B)
8. IGNORE any relationships not explicitly stated in the sentence

Examples:
Sentence: "A because B and C. C caused E and D."
Keywords: [A, B, C, D, E]
Output:
B -> A
C -> A
C -> E
C -> D

Sentence: "Flooding occurred due to heavy rain and poor drainage, which then caused crop damage."
Keywords: [rain, drainage, flood, crop]
Output:
rain -> flood
drainage -> flood
flood -> crop

---
Analyze THIS sentence:
"{sent}"

Output ONLY the causal pairs (no other text):
"""
```

The model is set its temperature to low (0.2) to reduce randomness.

The model will return up to 300 tokens of plain causal statements (e.g., X -> Y).
Takes documents (as lists of tokens) and joins them into sentences.

**Output Processing**
The model output is cleaned and normalized:

- Keyword variants are matched against the predefined keyword list.

- Only exact or partial matches are retained.

- Valid causal pairs are stored with frequency counts.

For instance, if “crop damage” is detected but only “crop” is in the keyword list, the system maps the phrase to “crop” to preserve consistency.

**Graph Construction**
The extracted causal pairs are added to a Directed Acyclic Graph (DAG) structure:

- Each node represents a concept (e.g., “weather” or “fatigue”).

- Each edge represents a directional cause-effect relation (e.g., “weather” → “delay”).

Edges can carry weights indicating how often a particular causal link appeared across different reports.

### Exemple of usage 

```python
if __name__ == "__main__":
    # Aviation safety keywords
    keywords = [
        'crack', 'passenger', 'turbulence', 
        'attendant', 'seat', 'taxi', 'crew', 'fuel', 'power', 
        'engine', 'fire', 'weather', 'runway', 'controller', 
        'traffic', 'captain', 'pushback', 'nose', 'door', 
        'gear', 'landing', 'blade', 'takeoff', 
        'pilot', 'aircraft', 'collision', 'landing'
    ]
    
    # Sample aviation incident reports (tokenized)
    documents = [
        "The engine failure caused loss of power during takeoff".split(),
        "Pilot fatigue contributed to runway excursion".split(),
        "Severe weather led to turbulence and passenger injuries".split(),
        "Crack in the blade resulted in engine fire".split(),
        "Controller poor communication cause pilot bad judment leading to collision".split(),
        "Improper pushback procedure caused nose gear damage".split(),
        "Fuel contamination led to engine power loss and emergency landing".split(),
        "Controller error resulted in traffic conflict on taxiway".split(),
        "Cabin door issue during flight led to emergency dlanding".split(),
        "Poor weather conditions contributed to collision with terrain".split()
    ]

    # Step 1: Extract causal relations from aviation reports
    print("Extracting causal relations from aviation reports...")
    causal_relations = extract_causal_relations(documents, keywords)
    print("Discovered aviation safety relations:", dict(causal_relations))

    # Step 2: Build the aviation safety network
    print("Building aviation safety network...")
    safety_net_graph = build_custom_dag(causal_relations)

    # Step 3: Extract all causal paths in aviation context
    paths = safety_net_graph.get_paths()
    print("\nAviation Safety Causal Paths:")
    for i, path in enumerate(paths, 1):
         print(f"{i}. {' → '.join(path)}")

    # Step 4: Visualize aviation safety network
    print("Generating aviation safety visualization...")
    safety_net_graph.visualize_graphviz('aviation_safety_before')
    print("\nAviation Safety Graph Structure:")
    print(safety_net_graph)

    # Step 5: Insert aviation-specific nodes
    safety_net_graph.insert_node(child='weather', cause_name='meteorological')
    print("\nAfter inserting 'meteorological' as parent of 'weather':")
    print(safety_net_graph)

    safety_net_graph.insert_node(parent='pilot', cause_name='accident')
    print("\nAfter inserting 'accident' as child of 'pilot':")
    print(safety_net_graph)

    # Adding intermediate factor between pilot and accident
    safety_net_graph.insert_node(parent='pilot', child='accident', cause_name='judgment')
    print("\nAfter inserting 'judgment' between 'pilot' and 'accident':")
    print(safety_net_graph)

    # Step 6: Remove a node
    safety_net_graph.remove_node(node='judgment')
    print("\nAfter deleting 'judgment':")
    print(safety_net_graph)

    # Additional aviation-specific analysis
    print("\nRoot causes in aviation system:")
    print(safety_net_graph.get_roots())
    
    print("\nFinal outcomes in aviation incidents:")
    print(safety_net_graph.get_leaves())

    safety_net_graph.visualize_graphviz('aviation_safety_after')
```

![Figure 2: Directed Acyclic Graph built before insertion/deletion operations](images/aviation_safety_before.png)

<small> Figure 2: Directed Acyclic Graph built before insertion/deletion operations</small>

![Figure 3: Directed Acyclic Graph built after insertion/deletion operations](images/aviation_safety_after.png)

<small> Figure 3: Directed Acyclic Graph built after insertion/deletion operations</small>

### Advantages of the LLM Approach
Traditional rule-based extraction methods rely on fixed rules (e.g., detecting words like “because,” “due to”) and struggle with complex sentence structures, especially when:

- Multiple causes or effects are described,

- Relationships span multiple clauses,

- Negations or uncertainties are involved.

By contrast, the LLM can interpret nuanced linguistic patterns and deliver structured output from natural sentences without requiring hard-coded rules. It generalizes across variations in grammar, phrasing, and report writing styles.

