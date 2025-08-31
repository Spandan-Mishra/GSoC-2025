# Diet Management in CARE

## Project Summary

The Diet Management module aims to bridge the gap between clinical care and nutritional health within CARE’s ecosystem. This module will empower patients to adjust their dietary plans, while also being able to monitor and track their eating habits. By integrating dietary data with existing patient records, CARE will unify medical and lifestyle interventions. My proposal gives detailed architectures for the Meal Plan Integration, Calorie Tracking, Nutritional Recommendations, and Patient Record Integration features. It comprises in depth descriptions, workflows and technologies that are needed to integrate the Diet Management module in CARE.

<br />

## Deliverables of the project

- Staff can create new Nutrition Products.
- List of allergens is available to select from, when creating a Nutrition Product.
-	Listing of all Nutrition Products, editing & viewing of individual ones.
-	Dieticians can create Nutrition Orders and select Nutrition Products based on the needs of the patient.
-	Existing allergies data for the patient is displayed during Nutrition Order creation.
-	List of Nutrition Orders for a particular encounter can be found under ‘Nutrition Orders’ encounter tab.
-	Canteen staff can see the list of all pending orders linked to their specific canteen location, along with the details of the order.
-	Canteen staff can update the status of an order, and once it is mark completed, it will be ready to deliver.
-	Nurses/Staff can log intake for the Nutrition Orders marked as completed by canteen staff, under the ‘Nutrition Orders’ encounter tab.
-	Intake Logging provides detailed information of intake like consumption status, consumption amount and reason for consumption.
-	The Diet Management module is integrated to work seamlessly with the main care application in encounters, questionnaires, location services, etc.

<br />

## Setup

Steps to setup the Diet Management module with the main Care application can be found here:
-	Care Diet Frontend: [care_diet_fe](https://github.com/Spandan-Mishra/care_diet_fe/blob/main/README.md)
-	Care Diet Backend: [care_diet_plugin](https://github.com/Spandan-Mishra/care_diet_plugin/blob/main/README.md)

<br />

## Flow of diet management

### 1. Nutrition Product Management

**Product Creation**

-	Staff can create nutrition products using the NutritionProductForm component located in NutritionProductForm.tsx
-	The form uses React Hook Form with Zod validation to capture product details like name, calories, quantity, and allergens
-	Allergen selection is handled by the AllergenMultiSelect component which fetches available allergens from the valueset API using valuesetApi.getValueSet()
-	Product data is submitted to the backend through dietApi.createNutritionProduct() which calls the Django REST API endpoint
-	The backend stores products in the NutritionProduct model with relationships to allergens and facilities

**Listing & Viewing**

-	NutritionProductList component displays all products in a table format using React Query for data fetching
-	Individual products can be viewed/edited through NutritionProductView which loads product details and allows modifications
-	The backend uses ProductViewSet to handle CRUD operations with proper serialization through ProductSerializer

### 2. Nutrition Order Management

**Order Creation**

-	Dieticians create nutrition orders through the NutritionOrderQuestion component integrated into CARE's questionnaire system
-	The component fetches patient allergies using allergyApi.getPatientAllergies() and displays them prominently for safety
-	Product selection uses the Autocomplete component which searches nutrition products via dietApi.listNutritionProducts()
-	Location selection pulls facility locations through a custom locationApi.list() function

**Order Submission**

-	Form submission captures order details including selected products, scheduling, and location
-	Patient allergies are captured at point-in-time and included in the order data for safety tracking
-	Order data is structured and submitted through updateQuestionnaireResponseCB() to integrate with CARE's questionnaire system
-	Backend processing occurs through EncounterNutritionOrderViewSet which creates NutritionOrder instances
Nutrition Orders Tab
-	Orders are displayed in encounters through the EncounterNutritionOrdersTab component
-	The tab shows all nutrition orders for a specific encounter using React Query to fetch data
-	Each order is rendered using NutritionOrderCard which displays order details, status, and allows actions


### 3. Canteen Dashboard

**Order Management**

-	Canteen staff access pending orders through LocationCanteenDashboard component
-	The dashboard filters orders by location and shows only those assigned to the staff's canteen
-	Order status updates (preparing, ready, completed) are handled through the dashboard interface
-	Backend uses location-based filtering in the viewsets to ensure staff only see relevant orders

### 4. Intake Logging

**Logging Interface**

-	Nurses log intake through the IntakeLoggingModal component accessible from the nutrition orders tab
-	The modal captures consumption details including amount consumed, consumption status, and reasons for non-consumption
-	Form validation ensures proper data entry before submission

**Data Flow**

-	Intake data is submitted through dietApi.createNutritionIntake()
-	Backend creates NutritionIntake records linked to the original nutrition orders
-	The system tracks completion status and updates order states accordingly