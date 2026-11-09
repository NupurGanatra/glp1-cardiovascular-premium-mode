# glp1-cardiovascular-premium-mode
How a short GLP-1 course changes cardiovascular risk, claims and premiums for diabetic adults (Python, Cox model).
A simulated cost-benefit model of a short GLP-1 course (0 to 2 years) for diabetic adults. It estimates how course length changes long-term cardiovascular risk and claim costs, and compares the premium with and without GLP-1. Built in Python with a Cox proportional hazards model.
## Question
For diabetic adults of different ages and genders who have just finished a GLP-1 course of 0 to 2 years, how does the course length change their long-term chance of cardiovascular disease over the following 5 to 10 years, and for which ages, genders and course lengths, if any, do the insurer's savings in future claims cover the cost of the drug?
## Model:
h= h0 * exp(B1*(age-30)+B2*gender+B3*d)
- d is the course length in years (0 to 2).
- age is the age at stopping.
- gender is 1 for male and 0 for female.
- d = 0 is the no-GLP-1 comparison (same person, GLP-1 term switched off).

From the hazard h, the probability of a cardiovascular event within T years is (1 - exp(-h * T)). The expected claim cost is that probability multiplied by the cost of an event. The net premium is the 10-year expected cost (claims plus drug cost for GLP-1 users) divided by 10, and the gross premium is the net premium divided by (1 - 0.15).

| Parameter | Value | Source |
| h0 | 0.006 | Assumed |
| B1 | 0.04 | Assumed |
| B2 | 0.18 | Assumed |
| B3 | -0.024 | `ln(0.88) / 5.4`, from the REWIND trial (hazard ratio 0.88, median follow-up 5.4 years) |
| Drug cost | $12,000 per year | Assumed |
| Cardiovascular event cost | $50,000 | Assumed |
| Loading | 15% of gross premium | Assumed |

h0, B1 and B2 are illustrative values chosen for this project, not estimated from data. B3 is derived from a published trial result: each year on the drug multiplies the hazard by exp(-0.024) = 0.976, a reduction of about 2.4% per year.

## Assumptions
 
1. The drug costs $12,000 per year of treatment, and a cardiovascular event costs $50,000 (first event only). Both are assumed.
2. The person does not restart GLP-1 after the course, which lasts 0 to 2 years.
3. No death or lapse occurs.
4. The study covers diabetic adults only.
5. The time value of money is ignored.
6. The health improvement from GLP-1 stays constant after the course ends.
7. The insurer pays for the drug and recovers it through the premium, which is level over 10 years with a 15% loading for expenses and profit.
8. The hazard is constant over time, and age is fixed at the start of observation. The parameters h0, B1 and B2 are assumed, and B3 is derived from a published trial.
9. The portfolio is simulated: 10,000 people with ages uniform from 30 to 70, an even gender split, and course lengths uniform from 0 to 2 years, all independent of each other (seed 50).


## Results
 
Average per person, from the simulated portfolio:
 
| | With GLP-1 | Without GLP-1 |
|---|---|---|
| 10-year expected claim cost | $7,244 | $7,405 |
| Gross premium per year | $2,275 | $871 |
 
The average claim saving over 10 years is about **$160** per person (about $88 over 5 years). The drug costs about $12,000 for each year of use, and course lengths average about 1 year.
 

 
![Claim cost over time including the drug cost](claims_plus_drug.png)
 
![Gross premium by age band](premium_by_age_band.png)
 
## Observations

1. **Claims are lower with GLP-1, but the drug costs far more than it saves.** Over 10 years, expected cardiovascular claims are about $7,244 with GLP-1 against $7,405 without, a saving of about $160. The average gross premium is therefore about $2,275 a year for GLP-1 users against $871 for non-users, almost all of the difference being the drug cost.
2. **The drug costs more than all the claims it could save.** The average expected cardiovascular claim cost over 10 years with no GLP-1 is $7,405, which is less than the drug cost of about $12,000. Even a drug that removed every cardiovascular claim would not pay for itself on these assumptions, so for no age, gender or course length do the claim savings cover the drug cost.
3. **The saving is the drop in risk multiplied by $50,000.** The drug lowers each person's risk by about the same percentage, but the same percentage of a bigger risk is a bigger drop. Older people start with a higher risk, so they save more. The saving is still small next to the drug cost at every age.
4. **Adverse selection.** If both groups are charged one pooled premium, non-users pay more than their own expected cost and GLP-1 users pay less than theirs. People who expect to take GLP-1 are then more likely to buy cover, so the insurer should ask about GLP-1 use at underwriting and price for it.
5. **The size of the premium gap depends on assumption 7.** If the insurer does not pay for the drug, the two premiums are almost equal, and the GLP-1 users' premium is slightly lower.

## Limitations
- Only cardiovascular claims are counted. Other benefits and costs of the drug, such as effects on kidney disease or weight, side effects and rebates, are left out.
- The portfolio is simulated, and GLP-1 use is random. In real data, people who take the drug differ from those who do not.
- B3 comes from a trial result in which people were still taking the drug, and the model applies it after the course ends.
- The conversion from the trial hazard ratio to a yearly B3 assumes the benefit builds up evenly each year.
- h0, B1 and B2 are assumed and not estimated from patient data.
- Possible next steps: a sensitivity analysis of B3 (for example -0.02 to -0.09), and fitting the model to real patient-level data.

## Sources
 
- REWIND trial of dulaglutide in type 2 diabetes: hazard ratio 0.88 for cardiovascular outcomes, median follow-up 5.4 years (source of B3).
- Background reading: Deo et al. (2022), time-varying cardio-protective effect of GLP-1 receptor agonists (pooled analysis of eight trials); Piccini et al. (2023), time-dependent effect of GLP-1 receptor agonists, real-world study.

