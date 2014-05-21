meteor-development-guidelines
=============================

#HTML

Wrong:

    <button class="btn btn-primary someOtherClass" someInformation="{{somethingInteresting}}">Add Points</button>

Correct:

    <button class="btn btn-primary some-other-class" data-some-information="{{someInformation}}">{{__ 'Add Points'}}</button>

- All attributes are in CSS-Style dash-separated.
- Attributes containing data should be named with a data prefix.
- Helpers should be camelcased like normal JavaScript conventions.
- Strings should always be warpped into a i18n wrapper/dummy function to make translations later on easy.

