# SPARQL Query Collection

Every query uses the namespace `PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>` unless otherwise stated. Replace the `VALUES` bindings with the resources that match your scenario. Use `f:AllAge` as a fallback age group whenever a spot is marked as suitable for everyone.

## 1. Tourist Condition + Age Group Accessibility

Example: Broken leg traveler in the Elder age group (needs wheelchair-friendly access).

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?neededFeature
WHERE {
  VALUES (?touristCondition ?ageGroup) { (f:BrokenLeg f:ElderAge) }
  ?touristCondition rdf:type f:TouristCondition ;
                    f:requireAccessibilityFeature ?neededFeature .
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasAccessibilityFeature ?neededFeature ;
        f:isSuitableForAgeGroup ?ageGroup .
}
```

## 2. Toddler-Friendly With Dietary Restrictions

Example: Traveling with a toddler (use `f:ChildAge`) who needs Halal dining options.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?travellerAge ?dietNeed) { (f:ChildAge f:Halal) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasVisitConstraint ?dietNeed ;
        f:isSuitableForAgeGroup ?ageCategory .
  FILTER(?ageCategory IN (?travellerAge, f:AllAge))
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
  VALUES (?ageGroup ?neededFeature) { (f:ChildAge f:WheelchairFriendly) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:isSuitableForAgeGroup ?ageGroup ;
        f:hasAccessibilityFeature ?neededFeature ;
        f:requireBudgetLevel ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## 4. Spot Type + Accessibility + Time Window + Duration

Example: Museums that are wheelchair-friendly, open year-round, and doable within two hours.

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

## 5. Must-See Spots For a Time Constraint (Rating >= 4)

Example: Weekend-only attractions with rating ? 4.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?rating
WHERE {
  VALUES ?timeRestriction { f:WeekendOnly }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasTimeRestriction ?timeRestriction ;
        f:hasRating ?rating .
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

Example: Royal Park Rajapruek.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT ?spot ?petAllowed
WHERE {
  VALUES ?spot { f:Royal_Park_Rajapruek }
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
  VALUES (?targetDistrict ?ageGroup ?dietNeedA ?dietNeedB) {
    (f:Mueang_Chiang_Mai f:ChildAge f:Halal f:Gluten-Free)
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

## 10. Activity + Cheap Budget For a Family in a District

Example: Sightseeing with the family in Hang Dong on a low budget.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot ?budgetLevel
WHERE {
  VALUES (?desiredActivity ?district) { (f:Sightseeing f:Hang_Dong) }
  ?spot rdf:type ?spotType .
  ?spotType rdfs:subClassOf* f:TouristSpot .
  ?spot f:hasActivity ?desiredActivity ;
        f:isInDistrict ?district ;
        f:isSuitableForAgeGroup f:AllAge ;
        f:requireBudgetLevel ?budgetLevel .
  FILTER(?budgetLevel IN (f:LowBudgetLevel, f:FreeBudgetLevel))
}
```

## 11. Dual Visit Constraints + Activity + Budget in a District

Example: Vegetarian + gluten-free dining with eating activity under a medium budget in Mueang Chiang Mai.

```sparql
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX f: <http://www.semanticweb.org/h99/ontologies/2025/8/chiang-mai-traveling-guide#>

SELECT DISTINCT ?spot
WHERE {
  VALUES (?district ?visitConstraintA ?visitConstraintB ?activityNeeded ?budgetLevel) {
    (f:Mueang_Chiang_Mai f:Vegetarian f:Gluten-Free f:Eating f:MediumBudgetLevel)
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
