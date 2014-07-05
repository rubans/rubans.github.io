---
layout: post
title: "Autocomplete Control Using Rx"
description: ""
category: 
tags: []
---

While attempting to implement a "Goggle search like" auto complete control, I looked at different ways of doing it. 
What I wanted was to have a Auto complete text box ( similar to Google Search) which would display possible results on each keystroke with some sort of minimum delay so that search is not executed until really necessary.
Using the Telerik RadAutoComplete WPF control, the usual way would have been to using event handlers to capture key strokes onkeydown event and then perform the search. However, using RX (Reactive Extensions) I found it's much simpler and able to achieve the objective with less code.

To start with, the original example (without Rx) is available here:
[http://blogs.telerik.com/xamlteam/posts/14-04-30/how-to-add-minimum-populate-delay-in-radautocompletebox-for-wpf-sliverlight](http://blogs.telerik.com/xamlteam/posts/14-04-30/how-to-add-minimum-populate-delay-in-radautocompletebox-for-wpf-sliverlight)

    	public MainWindow()
        {
            InitializeComponent();
            this.timer.Interval = TimeSpan.FromSeconds((this.DataContext as ViewModel).SelectedDelay);
            this.AutoCompleteBox.AddHandler(Control.KeyDownEvent, new KeyEventHandler(OnAutoCompleteBoxKeyDown), true);
        }

        private void OnAutoCompleteBoxSearchTextChanged(object sender, EventArgs e)
        {
            if (this.isDeleting || string.IsNullOrEmpty(this.AutoCompleteBox.SearchText))
            {
                this.AutoCompleteBox.Populate(string.Empty);
            }
            else
            {
                if (this.timer != null && this.timer.IsEnabled)
                {
                    this.timer.Stop();
                    this.timer.Start();
                }
                else
                {
                    if (this.timer != null)
                    {
                        this.timer.Start();
                        this.timer.Tick += OnTimerTick;
                    }
                }

                this.SetStatusBusyIndicator(true);
                this.DisabledOverlay.Visibility = System.Windows.Visibility.Visible;
            }

            this.isHandled = true;
        }

        private void OnTimerTick(object sender, EventArgs e)
        {
            this.timer.Stop();
            this.SetStatusBusyIndicator(false);
            this.DisabledOverlay.Visibility = System.Windows.Visibility.Collapsed;
            this.isHandled = false;
            if (!this.isDeleting)
            {
                this.AutoCompleteBox.Populate(this.AutoCompleteBox.SearchText);
            }
        }

        private void OnAutoCompleteBoxPopulating(object sender, CancelRoutedEventArgs e)
        {
            e.Cancel = this.isHandled;
        }

        private void OnAutoCompleteBoxKeyDown(object sender, KeyEventArgs e)
        {
            if (e.Key == Key.Back || e.Key == Key.Delete || e.Key == Key.Escape || (Keyboard.Modifiers == ModifierKeys.Control && e.Key == Key.X))
            {
                this.isDeleting = true;
                this.AutoCompleteBox.IsDropDownOpen = false;
                this.SetStatusBusyIndicator(false);
                this.DisabledOverlay.Visibility = System.Windows.Visibility.Collapsed;
            }
            else
            {
                if (e.Key != Key.Up && e.Key != Key.Down)
                {
                    this.AutoCompleteBox.IsDropDownOpen = false;
                    if (e.Key == Key.Enter && this.isDeleting)
                    {
                        this.isHandled = false;
                        this.AutoCompleteBox.Populate(this.AutoCompleteBox.SearchText);
                    }
                }

                this.isDeleting = false;
            }
        }
The original code which handled the onkey event handlers and search:




Using Reactive UI (based on Rx), I was able to refactor it down to:
    
    	public ViewModel()
        {
            //GetItems();
            this.delays = new ObservableCollection<int>()
            {
                1, 2, 3, 4, 5
            };
            this.selectedDelay = this.delays[1];

            var executeSearchCommand = new ReactiveCommand();
            var results = executeSearchCommand.RegisterAsyncFunction(s => { return ExecuteSearch(s as string); });
            _executeSearchCommand = executeSearchCommand;

            this.ObservableForProperty<ViewModel, string>("SearchText")
                .Throttle(TimeSpan.FromMilliseconds(800))
                .Select(x => x.Value)
                .DistinctUntilChanged()
                .Where(x => !string.IsNullOrWhiteSpace(x))
                .Subscribe(_executeSearchCommand.Execute);

            _searchResults = new ObservableAsPropertyHelper<ObservableCollection<Item>>(results, _ => raisePropertyChanged("SearchResults"));

        }
    
So you can cut down a lot of event handling code using Rx and reckon much easier to read without the need for multiple functions.

The full code sample is available on:
[https://github.com/rubans/gitprojects/tree/master/RadAutoCompleteExample/MinimumPopulateDelayRx](https://github.com/rubans/gitprojects/tree/master/RadAutoCompleteExample/MinimumPopulateDelayRx)