# SPARQL Query Collection

Every query uses the namespace `PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>` unless otherwise stated. Replace the `VALUES` bindings with the placeholder resources (e.g., `f:DISTRICT_INPUT`). Use `f:AllAge` as a fallback age group whenever a spot is marked as suitable for everyone.
## I have <xxx TouristCondition and yyy age group>. Which tourist spots in Chiang Mai are accessible to me? (Medium)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?neededFeature
WHERE {
  VALUES (?touristCondition ?ageGroup) { (f:TOURIST_CONDITION_INPUT f:AGE_GROUP_INPUT) }
  ?touristCondition rdf:type f:TouristCondition ;
                    f:requireAccessibilityFeature ?neededFeature .
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasAccessibilityFeature ?neededFeature ;
        f:isSuitableForAgeGroup ?ageGroup .
}
```

## I am travelling with my <toddler>, and he has <xxx diet restrictions>. Which Chiang Mai attractions are friendly and safe? (Medium)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?travellerAge ?dietNeed) { (f:AGE_GROUP_INPUT f:DIET_RESTRICTION_INPUT) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasVisitConstraint ?dietNeed ;
        f:isSuitableForAgeGroup ?ageCategory .
  FILTER(?ageCategory IN (?travellerAge, f:AllAge))
}
```

## For the <xxx age group>, what are the <TouristSpot> that are both accessible and affordable (low-cost or free)? (Medium)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?budgetLevel
WHERE {
  VALUES (?ageGroup ?neededFeature) { (f:AGE_GROUP_INPUT f:ACCESSIBILITY_FEATURE_INPUT) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isSuitableForAgeGroup ?ageGroup ;
        f:hasAccessibilityFeature ?neededFeature ;
        f:requireBudgetLevel ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## If I want to go to <TouristSpotType> that supports <AccessibilityFeatures> and I can enjoy it in <TimeDuration>, where should I go? (Hard)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?durationHours
WHERE {
  VALUES (?desiredType ?featureNeeded ?timeRestriction ?maxDurationHours) {
    (f:Museum f:WheelchairFriendly f:AllYear "2.0"^^xsd:decimal)
  }
  ?spot rdf:type ?actualType .
  ?actualType rdfs:subClassOf* ?desiredType .
  ?spot f:hasAccessibilityFeature ?featureNeeded ;
        f:hasTimeRestriction ?timeRestriction ;
        f:requireMinimumDuration ?durationHours .
  FILTER(?durationHours <= ?maxDurationHours)
}
```

## I can only visit in <TimeConstraints> in Chiang Mai. Which must-see (4+ rating) attractions can I visit efficiently? (Easy)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?rating
WHERE {
  VALUES ?timeRestriction { f:TIME_RESTRICTION_INPUT }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasTimeRestriction ?timeRestriction ;
        f:hasRating ?rating .
  FILTER(?rating >= 4.0)
}
ORDER BY DESC(?rating)
```

## What are the top 5 tourist attractions <TouristSpot> in Chiang Mai? (Easy)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?rating
WHERE {
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasRating ?rating .
}
ORDER BY DESC(?rating)
LIMIT 5
```

## Which <District> in Chiang Mai is best for <TouristSpotType>? (Easy)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT ?district (COUNT(DISTINCT ?spot) AS ?spotCount)
WHERE {
  VALUES ?requestedType { f:TOURIST_SPOT_TYPE_INPUT }
  ?spot rdf:type ?actualType .
  ?actualType rdfs:subClassOf* ?requestedType .
  ?spot f:isInDistrict ?district .
}
GROUP BY ?district
ORDER BY DESC(?spotCount)
```

## Can I bring a pet to <TouristSpot>? (Easy)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT ?spot ?petAllowed
WHERE {
  VALUES ?spot { f:TOURIST_SPOT_INPUT }
  OPTIONAL { ?spot f:isPetFriendly ?petAllowed }
}
```

## I'm a muslim child, and I am allergic to gluten, which <TouristSpot> in <District> that is cheap that I should visit. (Medium)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?targetDistrict ?ageGroup ?dietNeedA ?dietNeedB) {
    (f:DISTRICT_INPUT f:AGE_GROUP_INPUT f:VISIT_CONSTRAINT_INPUT_A f:VISIT_CONSTRAINT_INPUT_B)
  }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isInDistrict ?targetDistrict ;
        f:hasVisitConstraint ?dietNeedA ;
        f:hasVisitConstraint ?dietNeedB ;
        f:requireBudgetLevel ?budgetLevel ;
        f:isSuitableForAgeGroup ?ageCategory .
  FILTER(?ageCategory IN (?ageGroup, f:AllAge))
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## I want to <Activity> with a really cheap budget option with my family in <District>. What would be the best tourist spot? (Medium)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?budgetLevel
WHERE {
  VALUES (?desiredActivity ?district) { (f:ACTIVITY_INPUT f:DISTRICT_INPUT) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasActivity ?desiredActivity ;
        f:isInDistrict ?district ;
        f:isSuitableForAgeGroup f:AllAge ;
        f:requireBudgetLevel ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## Which tourist spots in <District> can host <A VisitContraint> and <B VisitContraint> simultaneously, offer <Activity>, and keep the budget at <budgetLevel>? (Very Hard)

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?district ?visitConstraintA ?visitConstraintB ?activityNeeded ?budgetLevel) {
    (f:DISTRICT_INPUT f:VISIT_CONSTRAINT_INPUT_A f:VISIT_CONSTRAINT_INPUT_B f:ACTIVITY_INPUT f:BUDGET_LEVEL_INPUT)
  }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isInDistrict ?district ;
        f:hasVisitConstraint ?visitConstraintA ;
        f:hasVisitConstraint ?visitConstraintB ;
        f:hasActivity ?activityNeeded ;
        f:requireBudgetLevel ?budgetLevel .
}
```
