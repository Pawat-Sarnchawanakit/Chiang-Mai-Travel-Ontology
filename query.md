# SPARQL Query Collection

Every query uses the namespace `PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>` unless otherwise stated. Replace the `VALUES` bindings with the resources that match your scenario.

## 1. Tourist Condition + Age Group Accessibility

Example: Broken leg traveler in the Adult age group.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?touristCondition ?ageGroup) { (f:BrokenLeg f:AdultAge) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasVisitConstriant ?touristCondition .
  ?spot f:hasVisitConstriant ?ageGroup .
}
```

## 2. Toddler-Friendly With Dietary Restrictions

Example: Traveling with a toddler who needs Halal dining options.

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

## 3. Accessible & Affordable Spots for an Age Group

Example: Child-friendly, wheelchair-accessible spots that are low-cost or free.

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

## 4. Spot Type + Accessibility + Two-Hour Time Window

Example: Museums that are wheelchair-friendly and doable in roughly two hours (approximated with `f:OneHour` slots).

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?desiredType ?featureNeeded ?timeWindow) { (f:Museum f:WheelchairFriendly f:OneHour) }
  ?spot rdf:type ?actualType .
  ?actualType rdfs:subClassOf* ?desiredType .
  ?spot f:hasAccessibilityFeature ?featureNeeded .
  ?spot f:hasVisitConstriant ?timeWindow .
}
```

## 5. Must-See Spots For a Time Constraint (Rating >= 4)

Example: Attractions visitable within one day and rated at least four stars.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?rating
WHERE {
  VALUES ?timeWindow { f:OneDay }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasVisitConstriant ?timeWindow .
  ?spot f:hasRating ?rating .
  FILTER(?rating >= 4.0)
}
ORDER BY DESC(?rating)
```

## 6. Top Five Tourist Attractions By Rating

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

## 7. District With the Most Spots of a Type

Example: District counts for cultural spots.

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

## 8. Pet-Friendly Check For a Specific Spot

Example: Jing Jai Market Chiang Mai.

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

## 9. Muslim Child, Gluten Allergy, Cheap, In a District

Example: Look within Mueang Chiang Mai for child-friendly, Halal + gluten-free, low-cost spots.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?targetDistrict) { (f:Mueang_Chiang_Mai) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isInDistrict ?targetDistrict .
  ?spot f:hasVisitConstriant f:ChildAge .
  ?spot f:hasVisitConstriant f:Halal .
  ?spot f:hasVisitConstriant f:Gluten-Free .
  ?spot f:hasVisitConstriant ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## 10. Activity + Cheap Budget For a Family in a District

Example: Exploring nature with a family in Mae Rim on a very low budget (assumes activities are encoded as visit constraints).

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?desiredActivity ?district) { (f:Explore_Nature f:Mae_Rim) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isInDistrict ?district .
  ?spot f:hasVisitConstriant f:AdultAge .
  ?spot f:hasVisitConstriant f:ChildAge .
  ?spot f:hasVisitConstriant ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
  ?spot f:hasVisitConstriant ?desiredActivity .
}
```

## 11. Mae Rim Spots for Wheelchair Users, Smokers, and Wildlife Watching

Example: Require wheelchair access, smoking rooms, wildlife watching, and low/free budget in Mae Rim.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isInDistrict f:Mae_Rim .
  ?spot f:hasAccessibilityFeature f:WheelchairFriendly .
  ?spot f:hasAccessibilityFeature f:SmokingRoom .
  ?spot f:hasVisitConstriant f:Wildlife_Warching .
  ?spot f:hasVisitConstriant ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```
