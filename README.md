# Cypress React Onboarding Component Test

The Onboarding Component test suite verifies the onboarding experience for new and returning users. It tests the component’s behavior in three states:

New user state — when the tutorial has not yet been completed.

Completing the tutorial — when the "Mark Tutorial as Completed" button is clicked, ensuring the UI updates and localStorage persists the state.

Returning user state — when the tutorial completion is already stored in localStorage.

These tests ensure correct UI rendering, state persistence, and proper interaction handling without relying on external mocking frameworks.

## React Onboarding Component



```bash
import { Button } from "@radix-ui/themes";
import { useState } from "react";

function Onboarding() {
  const [isTutorialCompleted, setTutorialCompleted] = useState(
    localStorage.getItem("tutorialCompleted") === "true"
  );

  const markTutorialCompleted = () => {
    localStorage.setItem("tutorialCompleted", "true");
    setTutorialCompleted(true);
  };

  return (
    <div>
      {isTutorialCompleted ? (
        <div>
          <h1>Welcome back!</h1>
          <p>You've already completed the tutorial.</p>
        </div>
      ) : (
        <div>
          <h1>Welcome to our app!</h1>
          <p>Complete the tutorial to get started.</p>
          <Button onClick={markTutorialCompleted}>
            Mark Tutorial as Completed
          </Button>
        </div>
      )}
    </div>
  );
}

export default Onboarding;

```


## Cypress Component Test



```bash
import { mount } from "cypress/react";
import Onboarding from "../../src/components/Onboarding";


describe("Onboarding Component", () => {
  beforeEach(() => {
    // Clear localStorage before each test
    localStorage.clear();
  });

  it("shows welcome message for new users", () => {
    mount(<Onboarding />);

    cy.contains("Welcome to our app!").should("exist");
    cy.contains("Complete the tutorial to get started.").should("exist");
    cy.contains("Mark Tutorial as Completed").should("exist");
  });

 it("marks tutorial as completed when button clicked", () => {
  mount(<Onboarding />);

  cy.contains("Mark Tutorial as Completed").click();

  cy.contains("Welcome back!").should("exist");
  cy.contains("You've already completed the tutorial.").should("exist");

  cy.window().then((win) => {
    expect(win.localStorage.getItem("tutorialCompleted")).to.equal("true");
  });
});


  it("shows completed state if tutorial already completed in localStorage", () => {
    localStorage.setItem("tutorialCompleted", "true");

    mount(<Onboarding />);

    cy.contains("Welcome back!").should("exist");
    cy.contains("You've already completed the tutorial.").should("exist");
    cy.contains("Mark Tutorial as Completed").should("not.exist");
  });
});

```

![Screenshot of Label Component](Screenshot%202025-09-27%20172704.png)

| Criteria                  | Justification                                                                                                                                                                                             |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Isolation**             | Each test runs independently by clearing `localStorage` in `beforeEach()`. This removes cross-test dependencies and ensures a consistent starting state.                                                  |
| **Mocking Quality**       | No external mocking libraries are used. The test uses real `localStorage` and Cypress’s DOM querying to verify component behavior, ensuring a realistic test environment.                                 |
| **Coverage**              | Covers all critical states of the component: <br>1) Initial onboarding view for new users. <br>2) State change when tutorial is completed. <br>3) Returning user view when tutorial is already completed. |
| **Readability**           | Tests are clearly structured with descriptive names for each case, making the behavior easy to understand and maintain.                                                                                   |
| **Clarity of Assertions** | Assertions clearly verify text content, button presence, state transitions, and `localStorage` values, ensuring behavior matches intended functionality without over-testing unrelated details.           |
| **Realistic Testing**     | By using `cy.window()` to access `localStorage` after UI changes, the test accurately simulates real user interaction and validates that state persistence works as expected.                             |
