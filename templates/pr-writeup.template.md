Add ${OPERATOR NAME} CSV and Package for OLM integration and deployment of operator through OperatorHub

- This pull requests introduces a CSV and Package which can be bundled together and used by the [Operator Lifecycle Manager](https://github.com/operator-framework/operator-lifecycle-manager) to install, manage, and upgrade the Jaeger Operator in a cluster.

- The ${OPERATOR NAME} operator versions available to all Kubernetes clusters using OLM can be updated by submitting a pull request to the [Community Operators GitHub repo](https://github.com/operator-framework/community-operators) that includes the latest CSVs, CRDs, and Packages.

- The CSV was created based of the [documentation provided by OLM](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/Documentation/design/building-your-csv.md) and the [documentation](https://github.com/operator-framework/community-operators/blob/master/docs/marketplace-required-csv-annotations.md) provided by [OperatorHub](https://github.com/operator-framework/operator-marketplace).

- The operator and the CSV were tested using the [scorecard functionality recently introduced](https://github.com/operator-framework/operator-sdk/pull/758) to the [Operator-sdk](https://github.com/operator-framework/operator-sdk). Currently, the ${OPERATOR NAME} operator scores a ${SCORECARD SCORE} on the scorecard tests.

OLM integration could be improved by future code changes to the Operator:
- Addressing the operator-sdk scorecard tests that failed.
- Adding additional information to the Jaeger CSV based on the [CSV documentation provided by OLM](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/Documentation/design/building-your-csv.md)
- Adding additional information to the Jaeger CSV based on the [CSV documentation provided by OperatorHub](https://github.com/operator-framework/community-operators/blob/master/docs/marketplace-required-csv-annotations.md)
