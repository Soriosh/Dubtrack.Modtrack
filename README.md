# Dubtrack.Modtrack
Derp
Copy Pasta This: 


(function() {
    'use strict';

    window.Modtrack = (function (Dubtrack) {
        var self = {};

        var settings = {
            downdub_notifications: true,
            downdub_thresholds : {
                501: 20, 251: 15, 101: 10, 0: 5
            },
            messages: {
                stream_countdown: 'Nightblue Stream countdown: http://nightblue3.net ',
                stream_starting: '@djs *Stream is starting* :nb3hype: Queue songs at your own risk of getting skipped/removed. ~Nightcore/Nightstep, AMVs and songs that are too chill will be removed from the queue!~',
                stream_ending: '@djs The *stream is ending* now, please stick to ~off stream rules for songs~. The queue will be purged in a few minutes. :bingbad:',
                song_rules: {
                    off_stream: '*Songs*: ~EDM | Trap | Chill~, No NSFW, No ear rape, No Hardstyle, ~No Nightcore/Nightstep~, ~No AMVs~, No troll songs, ~Main language English~, ~No same songs within 4 hours~',
                    on_stream: '*Songs (on stream)*: No NSFW, No ear rape, No Hardstyle, ~No Nightcore/Nightstep~, ~No AMVs~'
                },
                queue_locked: '*The queue is locked now*, so ~only Resident DJs and above~ can play songs.',
                sub_sunday: 'It\'s *Sub Sunday* :nb3hype: Queue will be locked as long as Rabia streams.',
                read_rules: 'rules: https://github.com/nightbloo/nightbloo.github.io/wiki/Nightblue3-Community:-Rules ',
                language: 'Please keep the chat in English. You can PM users if you wanna speak another language.',
                no_spam: 'Please don\'t spam the chat!',
                skips_explained: 'We don\'t ask for *skips* here. Downvote and mute it instead. Asking for skips will result in a chat mute.',
                props_explained: '*Props* are given with `!props`. They are likes that are used in roulettes.',
                dubs_explained: 'Dubs are likes for songs you play or upvote.',
                dubplus: '> *Dub+* adds additional features to dubtrack. Tutorial: https://goo.gl/yMyHGI',
                gde: '> *gde* adds ~emotes~ to Dubtrack (even more than Dub+ does). Get it at https://gde.netux.ml/',
                css: '> *CSS* options for Dub+: https://imgur.com/a/MuolQ ',
                roulette_open: '@djs Roulette is open now :nb3hype: Type `!join` for a chance of a random spot in the queue!'
            }
        };

        var menuElements = {
            commands: {
                label: 'Commands',
                elements: {
                    props: {
                        type: 'command',
                        label: '!props',
                        handler: chatMessage,
                        arguments: ['!props', true, true]
                    },
                    history: {
                        type: 'command',
                        label: '!history (with ID)',
                        handler: safeHistoryCheck,
                        arguments: null
                    },
                    roulette_open: {
                        type: 'command',
                        label: 'Roulette open',
                        handler: chatMessage,
                        arguments: [settings.messages.roulette_open, true, true]
                    }
                }
            },
            warnings: {
                label: 'Warnings',
                elements: {
                    read_rules: {
                        type: 'command',
                        label: 'Read Rules!',
                        handler: chatMessage,
                        arguments: [settings.messages.read_rules, false]
                    },
                    language: {
                        type: 'command',
                        label: 'English Only',
                        handler: chatMessage,
                        arguments: [settings.messages.language, false]
                    },
                    spam: {
                        type: 'command',
                        label: 'Don\'t Spam',
                        handler: chatMessage,
                        arguments: [settings.messages.no_spam, false]
                    }
                }
            },
            explanations: {
                label: 'Explanations',
                elements: {
                    skips_explained: {
                        type: 'command',
                        label: 'Skips explained',
                        handler: chatMessage,
                        arguments: [settings.messages.skips_explained, true]
                    },
                    props_explained: {
                        type: 'command',
                        label: 'Props explained',
                        handler: chatMessage,
                        arguments: [settings.messages.props_explained, true]
                    },
                    dubs_explained: {
                        type: 'command',
                        label: 'Dubs explained',
                        handler: chatMessage,
                        arguments: [settings.messages.dubs_explained, true]
                    },
                    dubplus: {
                        type: 'command',
                        label: 'Dub+',
                        handler: chatMessage,
                        arguments: [settings.messages.dubplus, true]
                    },
                    gde: {
                        type: 'command',
                        label: 'gde',
                        handler: chatMessage,
                        arguments: [settings.messages.gde, true]
                    },
                    css: {
                        type: 'command',
                        label: 'CSS',
                        handler: chatMessage,
                        arguments: [settings.messages.css, true]
                    }
                }
            },
            stream: {
                label: 'Stream',
                elements: {
                    song_rules_off_stream: {
                        type: 'command',
                        label: 'Song Rules off stream',
                        handler: chatMessage,
                        arguments: [settings.messages.song_rules.off_stream, true]
                    },
                    song_rules_on_stream: {
                        type: 'command',
                        label: 'Song Rules on stream',
                        handler: chatMessage,
                        arguments: [settings.messages.song_rules.on_stream, true]
                    },
                    stream_countdown: {
                        type: 'command',
                        label: 'Stream Countdown',
                        handler: chatMessage,
                        arguments: [settings.messages.stream_countdown, true, true]
                    },
                    stream_starting: {
                        type: 'command',
                        label: 'Stream starting',
                        handler: chatMessage,
                        arguments: [settings.messages.stream_starting, true, true]
                    },
                    stream_ending: {
                        type: 'command',
                        label: 'Stream ending',
                        handler: chatMessage,
                        arguments: [settings.messages.stream_ending, true, true]
                    },                    
                    queue_locked: {
                        type: 'command',
                        label: 'Queue locked',
                        handler: chatMessage,
                        arguments: [settings.messages.queue_locked, true]
                    },
                    sub_sunday: {
                        type: 'command',
                        label: 'Sub Sunday',
                        handler: chatMessage,
                        arguments: [settings.messages.sub_sunday, true]
                    }
                }
            },
            settings: {
                label: 'Settings',
                elements: {
                    downdub_notifications: {
                        type: 'setting',
                        label: 'Downdub Notifications',
                        var: settings.downdub_notifications
                    }
                }
            }
        };

        function chatMessage (message, send, clear) {
            var element = $('#chat-txt-message');
            element.val((clear === true ? '' : element.val()) + message);
            if (send === true) {
                $('.pusher-chat-widget-send-btn').click();
            }
            element.focus();
        }

        function safeHistoryCheck () {
            var songInfo = Dubtrack.room.player.activeSong.get('songInfo');
            chatMessage('!history ' + songInfo.fkid + ' *(this song)*', true, true);
        }

        self.executeCommand = function (groupIndex, elementIndex) {
            var command = menuElements[groupIndex].elements[elementIndex];
            command.handler.apply(self, command.arguments);
        };

        var UI = (function (parent) {
            var self = {};

            function buildUI () {
                var container = $('<div class="modtrack-container"><h2>Modtrack</h2></div>');
                var menuContainer = $('<div class="menu-container"></div>').appendTo(container);
                for (var groupIndex in menuElements) {
                    var elementGroup = menuElements[groupIndex];
                    menuContainer.append('<h3>' + elementGroup.label + '</h3>');
                    for (var elementIndex in elementGroup.elements) {
                        var element = elementGroup.elements[elementIndex];
                        if (element.type == 'command') {
                            menuContainer.append('<button type="button" onclick="Modtrack.executeCommand(\'' + groupIndex + '\', \'' + elementIndex + '\')">' + element.label + '</button>');
                        } else if (element.type == 'setting') {
                            menuContainer.append('<button type="button" onclick="" disabled><i class="fi-' + (element.var === true ? 'check' : 'x') + '"></i> ' + element.label + '</button>');
                        }
                    }
                }
                menuContainer.perfectScrollbar();
                return container;
            }

            self.init = function () {
                console.log('init ui');
                $('head').append('<link rel="stylesheet" type="text/css" href="https://cdn.rawgit.com/Chr0nX/Modtrack/v0.3-fixed/modtrack.css" />');
                buildUI().appendTo('#main-room .right_section');
            };

            self.showSystemMessage = function (message) {
                $('.chat-main').append('<li class="system"><span>' + message + '</span></li>');
            };

            return self;
        })(self);

        var util = (function (parent) {
            var self = {};

            self.listenToDowndubs = function () {
                var thresholds = {
                    users: [],
                    downdubs: []
                };
                var lastNotified = null;

                for (var index in settings.downdub_thresholds) {
                    thresholds.users.push(parseInt(index));
                    thresholds.downdubs.push(settings.downdub_thresholds[index]);
                }
                if (thresholds.users[0] < thresholds.users[thresholds.users.length - 1]) {
                    thresholds.users.reverse();
                    thresholds.downdubs.reverse();
                }

                Dubtrack.Events.bind('realtime:room_playlist-dub', function (event) {
                    if (event.dubtype == 'downdub') {
                        var userCount = Dubtrack.room.users.rt_users.length;
                        for (var i = 0; i < thresholds.users.length; i++) {
                            if (userCount >= thresholds.users[i]) {
                                var downdubs = Dubtrack.room.player.activeSong.get('song').downdubs + 1; // add the downvote causing the event
                                var songId = Dubtrack.room.player.activeSong.get('song').songid;
                                if (downdubs >= thresholds.downdubs[i] && lastNotified != songId) {
                                    UI.showSystemMessage('Song has reached the downdub threshold!');
                                    Dubtrack.room.chat.mentionChatSound.play();
                                    lastNotified = songId;
                                }
                                return;
                            }
                        }
                    }
                });
            };

            return self;
        })(self);

        (function init () {
            $(document).ready(UI.init);
            if (settings.downdub_notifications === true) {
                util.listenToDowndubs();
            }
        })();

        return self;
    })(Dubtrack);
})();
