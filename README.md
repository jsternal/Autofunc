##Overview##
Autofunc is an experiment, a C# windows forms application that explores how to compose sequences of screens in a memory-efficient and dependency-injection-friendly fashion that's compatible with a single composition root.

Consider a simple case in which a main dialog creates two sub-dialogs and all three forms depend on external systems, like a database (represented below by the ISession instance):

* Main form: List Employees
** Child form 1: Edit employee history
** Child form 2: Edit employee goals

If we want to use constructor injection to supply the dependencies for all three of these forms, we have the following options:

1) Inject our container into the main form (boooo)

    public EmployeeListPresenter(Container container)

2) Inject a general-purpose form factory into the main form, where the factory itself has an instance of the dependency-injection container. I've seen factories like these take responsibility for navigation as well, creating a super-controller.

    public EmployeeListPresenter(ISession session, ApplicationController controller)

3) Inject self-sufficient, specialized factories into the main form. If you're committed to keeping a single reference to your DI container (in your composition root), this can get awkward as your factories require additional factories to satisfy their target's dependencies.

    public EmployeeListPresenter(ISession session, EditEmployeeHistoryPresenterFactory factory1, EditEmployeeGoalsPresenterFactory factory2)

4) Inject Funcs into your main form that can create fully realized subforms.

    public EmployeeListPresenter(ISession session, Func<EditEmployeeHistoryPresenter> func1, Func<EditEmployeeGoalsPresenter> func2)