
# SPARQL Query Collection

## 1. Subclass Relationship Overview

Example query showing every declared subclass relationship.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX owl: <http://www.w3.org/2002/07/owl#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide>

SELECT ?subject ?object
WHERE {
  ?subject rdfs:subClassOf ?object
}
```

## 2. Tourist Spots Matching Accessibility Feature and Age Group

Example query for Wheelchair Friendly accessibility and Child Age visitors.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?neededFeature ?visitorAgeGroup) { (f:WheelchairFriendly f:ChildAge) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasAccessibilityFeature ?neededFeature .
  ?spot f:hasVisitConstriant ?visitorAgeGroup .
}
```

## 3. Toddler-Friendly Spots with Dietary Restrictions

Example query for Toddler Age travelers who need Halal-friendly options.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?travellerAge ?dietNeed) { (f:ToddlerAge f:Halal) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasVisitConstriant ?travellerAge .
  ?spot f:hasVisitConstriant ?dietNeed .
}
```

## 4. Accessible and Affordable Tourist Spots Per Age Group

Example query for Child Age visitors needing Wheelchair Friendly spots that are low-cost or free.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?budgetLevel
WHERE {
  VALUES (?visitorAge ?neededFeature) { (f:ChildAge f:WheelchairFriendly) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasVisitConstriant ?visitorAge .
  ?spot f:hasAccessibilityFeature ?neededFeature .
  ?spot f:hasVisitConstriant ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## 5. Spots by Type, Accessibility Features, and Time Constraint

Example query for Museums that are Wheelchair Friendly and doable within One Day.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?desiredType ?featureNeeded ?timeWindow) { (f:Museum f:WheelchairFriendly f:OneDay) }
  ?spot rdf:type ?actualType .
  ?actualType rdfs:subClassOf* ?desiredType .
  ?spot f:hasAccessibilityFeature ?featureNeeded .
  ?spot f:hasVisitConstriant ?timeWindow .
}
```

## 6. Must-See Attractions Within a Time Constraint

Example query for attractions that fit into a One Day stay, sorted by rating.

```sparql

```

## 7. Top Tourist Attractions by Rating

Example query returning the five highest-rated tourist spots overall.

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

## 8. Districts Best Suited for a Tourist Spot Type

Example query counting how many Cultural Spots exist per district.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT ?district (COUNT(DISTINCT ?spot) AS ?spotCount)
WHERE {
  VALUES ?requestedType { f:CulturalSpot }
  ?spot rdf:type ?actualType .
  ?actualType rdfs:subClassOf* ?requestedType .
  ?spot f:isInDistrict ?district .
}
GROUP BY ?district
ORDER BY DESC(?spotCount)
```

## 9. Pet-Friendly Tourist Spot Check

Example query checking if Jing Jai Market Chiang Mai allows pets.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT ?spot ?petAllowed
WHERE {
  VALUES ?spot { f:Jing_Jai_Market_Chiang_Mai }
  OPTIONAL { ?spot f:isPetFriendly ?petAllowed }
}
```
