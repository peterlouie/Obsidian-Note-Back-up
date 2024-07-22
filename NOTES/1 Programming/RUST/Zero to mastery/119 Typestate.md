Time Created: 22 July 2024 16:53

Tags: #rust/typestate 

Typestates

  Leverage type system to encode state changes
  Implemented by creating a type for each state
    Use move semantics to invalidate a state
    Return next state from previous state
    Optionally drop the state
      Close file, connection dropped, etc
  Compile time enforcement of logic

Recap

  Typestates leverage the compiler to enforce logic
  Can be used for:
    Invalidating / consuming states
    Properly transitioning to another state
    Disallowing access to a missing resources

Demo
```rust
//Hiring employee process

#[derive(Debug)]
struct Employee<State> {
    name: String,
    state: State,
}

impl<State> Employee<State> {
    fn transition<NextState>(self, state: NextState) -> Employee<NextState> {
        Employee {
            name: self.name,
            state: state,
        }
    }
}

struct Agreement;
struct Signature;
struct Training;
struct FailedTraining {
    score: u8,
}
struct OnboardingComplete {
    score: u8,
}

impl Employee<Agreement> {
    fn new(name: &str) -> Self {
        Self {
            name: name.to_string(),
            state: Agreement,
        }
    }

    fn read_agreement(self) -> Employee<Signature> {
        self.transition(Signature)
    }
}

impl Employee<Signature> {
    fn sign(self) -> Employee<Training> {
        self.transition(Training)
    }
}

impl Employee<Training> {
    fn train(self, score: u8) -> Result<Employee<OnboardingComplete>, Employee<FailedTraining>> {
        if score >= 7 {
            Ok(self.transition(OnboardingComplete { score }))
        } else {
            Err(self.transition(FailedTraining { score }))
        }
    }
}

fn main() {
    let employee = Employee::new("Kevin");

    let onboarding = employee.read_agreement().sign().train(9);

    match onboarding {
        Ok(_complete) => println!("onboarding complete"),
        Err(failed) => println!("failed score: {:?}", failed.state.score),
    }
}

```

Activity
```rust
// Topic: Typestates
//
// Summary:
//   An airline wants to reduce the amount of lost luggage by
//   ensuring luggage is properly tracked.
//
// Requirements:
// * Implement a luggage tracking system using the typestate pattern
// * Each piece of luggage has a tracking id
// * Luggage goes through multiple states at the airport:
//   * Check-in        (passenger gives luggage to airport)
//   * OnLoading       (luggage is loaded onto correct plane)
//   * Offloading      (luggage is taken off plane at destination)
//   * AwaitingPickup  (luggage is at destination waiting for passenger pickup)
//   * EndCustody      (luggage was picked up by passenger)
// Notes:
// * Optionally use generics for each state

#[derive(Debug, Clone, Copy)]
struct LuggageId(usize);

struct Luggage(LuggageId);

struct CheckIn(LuggageId);
struct OnLoad(LuggageId);
struct Offload(LuggageId);
struct AwaitingPickup(LuggageId);

#[derive(Debug)]
struct EndCustody(LuggageId);

impl Luggage {
    fn new(id: LuggageId) -> Self {
        Luggage(id)
    }

    fn check_in(self) -> CheckIn {
        CheckIn(self.0)
    }
}

impl CheckIn {
    fn onload(self) -> OnLoad {
        OnLoad(self.0)
    }
}

impl OnLoad {
    fn offload(self) -> Offload {
        Offload(self.0)
    }
}

impl Offload {
    fn carousel(self) -> AwaitingPickup {
        AwaitingPickup(self.0)
    }
}

impl AwaitingPickup {
    fn pickup(self) -> (Luggage, EndCustody) {
        (Luggage(self.0), EndCustody(self.0))
    }
}

fn main() {
    let id = LuggageId(422);
    let luggage = Luggage::new(id);

    let luggage = luggage.check_in().onload().offload().carousel();

    luggage.pickup();

    //println!("{:?}", luggage.pickup());
}

```
