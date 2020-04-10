---
layout: post
title: "Python: OOP"
categories: Python
author: "Michał"
---



# OOP


```python
class Patient(object):
	''' Medical centre patient'''
	status = 'patient'
	def __init__(self,name,age):
		self.name = name
		self.age = age
		self.conditions = []

	def get_details(self):
		print(f'Patient record: {self.name}, {self.age} years.' \
		f' Current information: {self.conditions}.')

	def add_info(self,information):
		self.conditions.append(information)
```
Stworzenie obiektów: 
```python
steve = Patient('Steven Hughes',48)
abigail = Patient('Abigail Sandwick',32)
```



```python
class Infant(Patient):
	''' Patient under 2 years'''
	def __init__(self,name,age):
		self.vaccinations = []
		super().__init__(name,age)

	def add_vac(self,vaccine):
		self.vaccinations.append(vaccine)
		
	def get_details(self):
		print(f'Patient record: {self.name}, {self.age} years.' \
		f' Patient has had {self.vaccinations} vaccines.' \
		f' Current information: {self.conditions}.' \
		f'\n{self.name} IS AN INFANT, HAS HE HAD ALL HIS CHECKS?')
```
Stworzenie "dzieci":
```python
archie = Infant('Archie Fittleworth',0)    
archie.add_vac('MMR') 
```




```python
class Patient(object):
	''' 
	Attributes
	----------
	name: Patient name
	age: Patient age
	conditions: Existing medical conditions
	'''
	status = 'patient'
	
	def __init__(self,name,age):
	self.name = name
	self.age = age
	self.conditions = []

	def get_details(self):
		print(f'Patient record: {self.name}, {self.age} years.' \
		f' Current information: {self.conditions}.')

	def add_info(self,information):
		self.conditions.append(information)
```