meteor-development-guidelines
=============================

I love working with other Meteor developers for my projects. In order to always get the best result and a code base that is always easy to follow, please read this guidelines and apply those to the code.

Thanks!

**Table of Contents**
- [Template/Html](#templates-and-html)
- [CSS and LESS](#css-and-less)
    - [LESS - Structure](#less-structure)
- [Template-Manager](#template-manager)
    - [Helpers](#helpers)
    - [Events](#events)


#Templates and Html

Wrong:

    <button class="btn btn-primary someOtherClass" someInformation="{{somethingInteresting}}">Add Points</button>

Correct:

    <button class="btn btn-primary some-other-class" data-some-information="{{someInformation}}">{{__ 'Add Points'}}</button>

- All attributes are in CSS-Style dash-separated.
- Attributes containing data should be named with a data prefix.
- Helpers should be camelcased like normal JavaScript conventions.
- Strings should always be warpped into a i18n wrapper/dummy function to make translations later on easy.

#CSS and LESS

Always use less, never pure CSS.

##Less-Structure

    /client
    /client/stylesheets/
    
    // Main less file, import all others
    // Import e.g. bootstrap, fontawesome... here
    // The only .less file!
    /client/stylesheets/style.less
    
    // override twitter bootstrap (etc.) variables
    /client/stylesheets/variables.import.less
    
    // basic global styles at stylesheet-root
    /client/stylesheets/typography.import.less
    /client/stylesheets/layout.import.less
    /client/stylesheets/...
    
    // Special styles for only a site
    /client/stylesheets/sites/
    /client/stylesheets/sites/home.import.less
    /client/stylesheets/sites/...

    // Styling for components
    /client/stylesheets/components/
    /client/stylesheets/components/rotatingBox.import.less
    /client/stylesheets/components/...

- Read: [Meteorjs and twitter bootstrap](http://www.manuel-schoebel.com/blog/meteorjs-and-twitter-bootstrap---the-right-way)

Wrong:

    my-less-file.lessimport

Correct:

    myLessFile.import.less


#Template-Manager

##Helpers

Wrong:

    Template.templateName.someHelper = function(){
        var post = Posts.findOne({...});
        return post.name;
    }
    
Correct:

    Template.templateName.helpers({
        someHelper: function(){
            var post = Posts.findOne({...});
            return post && post.name;
        },
        ...
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
