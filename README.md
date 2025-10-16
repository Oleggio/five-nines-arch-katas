# Oreilly' Architectural Katas Q4 2025: AI-Enabled Architecture
> This repository contains the artefacts for an Architecture Kata exercise.  
> It is structured to make it easy for reviewers to follow the reasoning, design decisions, and resulting architecture.

---

## 🧭 Table of Contents

- [Overview](#-overview)
  - [About the Project](#-about-the-project)
  - [Team](#-team)
- [Objectives](#-objectives)
- [Repository Structure](#-repository-structure)
- [How to Review](#-how-to-review)

---

## 📌 Overview

This repository documents the end-to-end architecture thinking process for the given 2025 kata scenario prepared by **team**.  
It focuses on:
- Clear articulation of **requirements**
- Well-structured **design decisions**
- Concise and visual **high-level design**

The aim is to provide **transparency, traceability, and clarity** for reviewers and collaborators.

---

### 👨‍💻 About the Project

This project is intended to help MobilityCorp achieve its business goals and address its biggest business challenges by offering the most optimal technology stack and architectural solution. The company's goals are: increase sales and revenue, expand market coverage, improve user experience, strengthen its market position. **Objectives** of the current project will serve as key enablers for these goals. 

### 👨‍💻 Team

The project was prepared by the team called 'Five Nines' consisting of:

- **[Oleh Yermilov](https://www.linkedin.com/in/oleg-yermilov-49a389113/)** - Lead Architect
- **[Oleksandra Tytar](https://www.linkedin.com/in/otytar/)** - Business Analyst
- **[Dmitry Zinkevich](https://www.linkedin.com/in/zinkevich/)** - Developer Lead
- **[Piotr Zyskowski](https://www.linkedin.com/in/piotr-zyskowski-80588329/)** - 
- **[Bo Connolly](https://www.linkedin.com/in/boconnolly/)** - DevOps


## 🎯 Objectives

Design from scratch (green field architecture) functionality for:
- **User Dialogue** - enhanced sales via user-personalized companionship, adaptive route/experience/pricing/charging advise, and driving compliance guidance
- **Dynamic Pricing** - sales support with more competitive pricing, addresses expansion ambitions and retention goals
- **Demand Forecasting** - uses internal and external data (weather, traffic, events) to forecast demand
- **Maintenance Optimization** (cost reduction) via:
  - Operational efficiency (location/fleet/route supply optimisation, transportation/charge task assignment)
  - Load distribution (even rental/usage among vehicles)
  - Maintenance prediction (sensor data - e.g. battery)
 
  , while ensuring continuous and smooth system operation of the existing core rental functionality.
---

## 📁 Repository Structure

- `adrs/` → Architecture Decision Records 
- `requirements/` → Business & technical requirements
- `hld/` → High Level Design artefacts (diagrams, docs)

---

## 📝 How to Review

1. **Start with** `requirements/` to understand the problem space.  
2. **Review** `adrs/` to follow the decision trail.  
3. **Explore** `hld/` for high-level architecture views and solution outline.  
4. Return here for **conclusion and summary** (to be added later in the kata lifecycle).

_This is a living document. It will evolve as the kata progresses._
