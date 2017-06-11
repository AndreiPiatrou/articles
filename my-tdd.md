## What is TDD for me?
[TDD][link_tdd] (Test Driven Development) is an awesome practice that helps me, and I hope you as well, to:
1. Keep calm, write a well-designed and structured code by SOLID principles.
2. Deliver products without a lot of bugs.
3. Learn and improve your own coding skills.
4. Make sure that nobody will break your code and product in future.
5. Do not debug at all because we do know how code works through tests.
6. Document code.

### Existing misunderstanding 
But there are some points that a lot of developers are complaining about:

1. They don't understand the fact that tests need for them and only for them but not for managers, business owners and target users.
2. They spend *extra* time on writing tests for the code that really works just for passing test coverate quality gates and they doesn't like this fact (usually this quality gates are created by team-leader or dictated by managers that know about how tests can improve product quality and as a result deliver quality product and they are right).
3. They don't want to write tests first and write tests overall because they don't know (yet) how to write a good test that can be really reliable.

All this problems appears because of developers have not written tests before and don't know the power of tests. It definetely can be fixed by a bit of theory and practice. 

## Alternative principle
I have found [wonderful article][link_yegor_tdd] in the internet that is titled as "The TDD that works for me". In short it says that tests shouldn't be written for code itself but definelty should be written for *every* bug that was reported. As a benefit you get: short PoC/MVP development time and developers that can concentrate just on coding without additional testing yourself.

### How can it work?
I think that this principle works for its author because of every developer has really great expirience and skills to write working code without testing themselfs and every this developer fills its *own responsibility* for what he or she is pushing to the repository and the main reason is that every existing bug in a result product doesn't cost a lot for product owners.

## My "The TDD that works for me"
I should say that I don't use the pure TDD every single time. When I have clear understanding of what I have to write I just write a lot of code but after and that I cover all my code by tests. When I don't know exaclty how to write some class or function I write a test that describes expected result of target function and it helps me to understand what dependencies and signatures this function can have and then I write the code.
I want to say that TDD is a great practice but you have not to follow this every single time like any another tool. TDD is a key to many doors and its depends on you what door you want to open with TDD.

[link_tdd]: https://en.wikipedia.org/wiki/Test-driven_development
[link_yegor_tdd]: http://www.yegor256.com/2017/03/24/tdd-that-works.html
