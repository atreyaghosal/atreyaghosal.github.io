---
title:  "Centaurus: Teacher-Centered Adaptive Education"
layout: post
categories: misc
---

Centaurus is a classroom management and lesson planning system that uses AI to reduce the cognitive load of teachers in the classroom. Centaurus is built around the idea that a teacher knows their classroom- Centaurus helps them adapt their lesson plans to it. Centaurus also supports IEPs, with specific modules for grounding lesson plan adaptations in IEP documents.

## A Custom Harness for Lesson Planning

For Centaurus, we built a harness specifically for the tasks of lesson planning and adaptation. It parses the plan, works out each student's access needs, and then adapts the plan one segment at a time.

### Step 1: Parse the lesson plan

1. Break the lesson plan into segments.
2. Seek clarification on unfamiliar terms.
3. Store the clarified terms and segments for that lesson plan in a cache.

### Step 2: Isolate the access need

Centaurus refers to the teacher's request and to student records, including IEPs, and identifies the access constraints. For example, a student who missed the previous week of classes might not be familiar with the material.

Identifying IEP-based access needs matters in particular, because teachers in some school districts are required to log IEP accommodations for individual students.

### Step 3: Adapt each lesson segment individually

Each segment is adapted on its own, keeping it faithful to the original lesson plan.
