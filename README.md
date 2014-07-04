meteor-development-guidelines
=============================

**Table of Contents**
- [Code Structure](#code-structure)
- [Naming Conventions](#naming-conventions)
- [Template/Html](#templates-and-html)
- [CSS and LESS](#css-and-less)
    - [LESS - Structure](#less-structure)
- [Template-Manager](#template-manager)
    - [Helpers](#helpers)
    - [Events](#events)
- [Processing User Input](#processing-user-input)
- [Common Best Practices](#common-best-practices)
    - [Login Restricted Routes](#login-restricted-routes)
    - [SEO](#seo)

##Code Structure
    /client
    /client/main.coffee
    /client/lib
    /client/router/config.coffee
    /client/router/onAfterAction.coffee
    /client/router/onBeforeAction.coffee
    /client/stylesheets/style.less
    /client/stylesheets/components/someComponent.import.less
    /client/stylesheets/sites/aSpecialSite.import.less
    /client/views
    /client/views/aSpecialSite/someFunctionality.html
    /client/views/aSpecialSite/someFunctionality.coffee
    /client/views/components/someComponent/someComponent.html
    /client/views/components/someComponent/someComponent.coffee
    /collections
    /lib/utils.coffee
    /packages
    /public
    /router/routes.coffee
    /server/publications/collectionName.coffee
    /server/startup
    /startup

##Naming conventions
MongoDB Fields: Underscore and short

Wrong:
    
    {
        nameOfTheUser: 'hans',
        theCurrentSignupState: 1
    }

Correct:

    {
        name: 'hans',
        signup_state: 1
    }

##Templates and Html

Wrong:

    <button class="btn btn-primary someOtherClass" someInformation="{{somethingInteresting}}">Add Points</button>

Correct:

    <button class="btn btn-primary some-other-class" data-some-information="{{someInformation}}">{{__ 'Add Points'}}</button>

- All attributes are in CSS-Style dash-separated.
- Attributes containing data should be named with a data prefix.
- Helpers should be camelcased like normal JavaScript conventions.
- Strings should always be warpped into a i18n wrapper/dummy function to make translations later on easy.

##CSS and LESS

Always use less, never pure CSS.

###Less-Structure

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


##Template-Manager

###Helpers

Wrong:

    Template.templateName.someHelper = ->
        post = Posts.findOne({...})
        post.name
    
Correct:

    Template.templateName.helpers {
        someHelper: ->
            post = Posts.findOne({...})
            return post?.name
        ...
    }

- always use helpers-object to define a single or multiple template helpers
- make sure you [guard variables](https://dweldon.silvrback.com/guards), since it is not 100% sure the data you reference really exists, most of the time. Just do it always.

###Events

Wrong:

    Template.templateName.events {
        'submit form': (e) ->
            // do s.th.
    }
    
Correct:

    Template.templateName.events {
        'submit form': (evt, tpl) ->
            // do s.th.
    }
    
- Always pass the event and template parameter in the form of "evt" and "tpl" even though you might not need those

##Processing User Input

1. Create a form
2. Get form data
3. Pass to a method
4. Validate
5. Pass errs
6. Show errs


We use this [forms package](https://github.com/DerMambo/mambo-forms/blob/master/form.html)

    <template name="someForm">
        {{>renderForm formOptions}}
    </template>
    
    Template.someForm.helpers({
        formOptions: ->
            formFields: [
                inputName: 'username'
                label: 'Your Username'
                type: 'text'
            ,
                inputName: 'favouriteColor'
                label: 'Your favourite color'
                type: 'text'
            ]
            actions: [
                label: 'Submit'
                btnClass: 'default'
                type: 'submit'
            ]
    })

    Template.someForm.events {
        'submit form': (evt, tpl) ->
            // comes from [common helpers](https://github.com/DerMambo/common-helpers)
            Etc.prevent evt
            
            // remove Form errors if there were some set before
            Form.removeFormError()
            
            // comes from [common helpers](https://github.com/DerMambo/common-helpers)
            data = $(evt.currentTarget).serializeObject()
            
            Meteor.call 'updateUser', @_id, data, (err) ->
                // function available that sets the errs to the form
                return handleFormError err if err
                Notify.setSuccess 'Saved'
    }


    // In User Collection
    Meteor.methods {
        updateUser: (_id, data) ->
        
        check _id, String
        check data, {
            username: String
            favouriteColor: String
        }
        
        // User model validates all fields
        errors = User.validate data
        // Stringified errors can be processed by handleFormError function above
        throw new Meteor.Error 400, JSON.stringify errors unless _.isEmpty errors
        
        // Using [minimongoid](https://github.com/Exygy/minimongoid)
        user = User.first {_id: _id} 
        // __ is a i18n dummy package that simply returns the string
        // but makes it easier adding i18n capabilities later on
        throw new Meteor.Error 404, __ 'Not found' unless user
        
        // add some data
        // validate some more logic (e.g. isAllowed?)
    
        // if everything is fine, update doc using minimongoid
        user.save data
    }
    
##Common Best Practices
###Login Restricted Routes
[Article](http://www.manuel-schoebel.com/blog/login-required-for-an-url-done-right)
###SEO
[Article](http://www.manuel-schoebel.com/blog/meteor-and-seo)
