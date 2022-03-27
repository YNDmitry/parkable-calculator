
# Parkable calculator

A little documentation of the code to make it easier to understand

All the logic is in a component named **AppTabsTab1.vue**

In order to publish the changes, enter the command **npm run deploy**

## Value name = key in the code

### Calculator inputs
1. Total bays --- TotalBays (❗️ To change the values of [Visitor Bays, Casual Bays, Allocated Bays] - go to the component AppBigSlider.vue)
2. We will charge (daily) (%) --- rateSlider.value
2. Flexible working --- working
3. SaaS bay charging --- SBCharing
4. Visitor bay charging --- VBCharing
5. Agency fee (%) --- AgencyFee
6. Working days per year --- WDPerYear
7. Leave + sick days per year --- LSDaysPerYear
8. Annual WFH days --- AWFHDays
9. Annual work days (less sick/leave/WFH) --- setAWorksDays (**Computed**)
10. Sales tax (%) --- SalesTax
11. Days open for casual sessions --- setDOFCSession (**Computed**)
12. Days open from underused Allocated --- setDOFUAllocated (**Computed**)
13. Total days open for casual sessions --- setTDOFCSession (**Computed**)
14. Total avaliable casual parking sessions --- setTACPSession (**Computed**)
15. Uptake on casual parking sessions (%) --- UOCPSession
16. Gross revenue on casual parking sessions --- setRevenue (**Method**)

### Under-used parks

1. Days/year each allocated park is unused --- setDYEAPIUnused (**Computed**)
2. Allocated parks unnused percentage --- setAPUPercentage (**Computed**)
3. Equivilent additional allocated parks --- setEAAParks (**Method**)
4. Equivilent additional parks --- setEAAParks (**Method**)
5. Total capacity increase (%) --- setTCIncrease (**Computed**)
