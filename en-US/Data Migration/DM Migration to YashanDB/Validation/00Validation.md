

The verification phase is used to check whether the data on the source side and the target side are consistent. YMP provides three modes: full verification, primary key verification, and statistical verification. Different verification strategies can be adopted according to the data volume and business importance:

|Data Volume |Business Importance |Recommended Validation Strategy |
| --------------- | ---------- | ------------ |
| Small (<100GB)   | High                | Full Validation                 |
| Small (<100GB)   | Low                 | Full Validation                 |
| Medium (100GB-1TB)| High                | Full Validation                 |
| Medium (100GB-1TB)| Low                 | Statistical Validation           |
| Large (>1TB)     | High                | Full Validation                 |
| Large (>1TB)     | Low                 | Statistical Validation           |

Consistency check is mainly used for data verification after full migration. Users can also create a separate validation task. The main difference between the two lies in the range of optional validation targets.

YMP provides user interfaces for various configurations, monitoring, and exception handling required during the consistency check process, and users can also follow the guidance in the documents below:

- [Validation Initialization](Validation Initialization): The validation initialization phase is the preparation stage for the validation task, mainly completing the configuration initialization of the validation task.
- [Consistency Validation](Consistency Validation): Execute consistency check between the source and target ends based on the configuration.


