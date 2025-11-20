# 02_plan_builder.md

## Plan Builder (Options → Days)

### Candidate Activities
Create a short list of candidate activities (e.g., attractions, restaurants, parks).  
Each activity must include:

- Type (attraction, restaurant, park, event, etc.)  
- Estimated duration  
- Cost range (currency, min–max)  
- Distance/travel time from lodging and previous slot  
- Indoor/Outdoor tag (for weather adaptation)  
- Open hours and reservation requirements  
- Accessibility/dietary flags  
- Popularity/priority score  

### Day-Building Loop
For each day:

1. **Morning activity**  
   - Near lodging (≤ 20 minutes travel).  
   - If lodging location is missing, default to city center or prompt user to define a radius.  

2. **Midday activity**  
   - Close by (≤ 30 minutes from Morning).  
   - Align with mealtime needs.  

3. **Afternoon activity**  
   - Different theme from earlier slots.  
   - Respect open hours and daily duration cap.  

4. **Evening restaurant or optional event**  
   - Match dietary preferences and reservation needs.  
   - Proximity to Afternoon or lodging.  

### Robustness Rules

- **Missing Data Handling**  
  - Lodging location missing → default to city center or prompt for radius.  
  - Transport mode unknown → assume walking + public transit with conservative travel times.  

- **Negative or Zero Budgets**  
  - If budget ≤ 0 → switch to free/low-cost activities, mark paid options as optional.  
  - Report daily estimated spend = 0–minimal.  

- **Weather Adaptation**  
  - Tag activities indoor/outdoor.  
  - If adverse weather expected → prioritize indoor, relegate outdoor to optional.  
  - Maintain a swap list for each slot.  

- **Time and Fatigue Controls**  
  - Cap total daily activity duration at 6–8 hours.  
  - Include rest windows.  
  - Limit consecutive long-duration activities.  

- **Fallbacks When Options Are Sparse**  
  - If a slot lacks candidates → insert flexible buffer, scenic walk near lodging, or cafe/market open at that time.  
  - Mark slot as “open choice” with nearby suggestions.  

- **Conflict Resolution**  
  - If duplicates appear across slots/days → prioritize first-day placement or highest-compatibility slot, then remove duplicates.  
  - If two activities compete for the same slot → select highest score within constraints, place the other as “alternate.”  

### Output
- Per-day summary with slot, activity, travel time, cost estimate, reservation note, and alternates.  
- Daily totals: cost, duration, distance.  
- Flags: weather-sensitive, accessibility concerns, booking requirements.  

---

### Pseudocode Example

```python
for each day in trip:
    slots = [Morning, Midday, Afternoon, Evening]
    for slot in slots:
        candidates = filter(options,
            open_in_slot(slot) and
            within_travel_time_threshold(slot) and
            fits_budget_today and
            respects_theme_diversity and
            matches_prefs
        )
        if candidates:
            pick_best(candidates)
            add_buffer()
        else:
            insert_fallback(slot)
    compute_daily_totals()
    add_alternates_for_weather_and_reservations()
