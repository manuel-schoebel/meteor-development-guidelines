meteor-development-guidelines
=============================

#Template/Html

Wrong:

    <button class="btn btn-primary someOtherClass" someInformation="{{somethingInteresting}}">Add Points</button>

Correct:

    <button class="btn btn-primary some-other-class" data-some-information="{{someInformation}}">{{__ 'Add Points'}}</button>

- All attributes are in CSS-Style dash-separated.
- Attributes containing data should be named with a data prefix.
- Helpers should be camelcased like normal JavaScript conventions.
- Strings should always be warpped into a i18n wrapper/dummy function to make translations later on easy.


#Template-Manager

##Helpers

Wrong:

    Template.templateName.someHelper = function(){
        var post = Posts.findOne({...});
        return post.name;
    }
    
Correct:

    Template.templateName.helpers({
        var post = Posts.findOne({...});
        return post && post.name;
    });

- always use helpers-object to define a single or multiple template helpers
- make sure you [guard variables](https://dweldon.silvrback.com/guards), since it is not 100% sure the data you reference really exists, most of the time. Just do it always.

##Events

Wrong:

    Template.templateName.events({
        'submit form': function(e){
            // do s.th.
        }
    })
    
Correct:

    Template.templateName.events({
        'submit form': function(evt, tpl){
            // do s.th.
        }
    })
    
- Always pass the event and template parameter in the form of "evt" and "tpl" even though you might not need those
