
# SPARQL Query Collection

## 1. Subclass Relationship Overview

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

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?rating
WHERE {
  ?spot rdf:type ?spotType.
  ?spotType rdfs:subClassOf* f:TouristSpot.
  ?spot f:hasRating ?rating.
  ?spot f:hasVisitConstriant f:OneDay.
}
ORDER BY DESC(?rating)
```

## 7. Top Tourist Attractions by Rating

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
