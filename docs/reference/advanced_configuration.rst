Advanced Configuration
======================

Full configuration options:

.. configuration-block::

    .. code-block:: yaml

        # Default configuration for extension with alias: "sonata_notification"
        sonata_notification:

            # Other backends you can use:
            #
            # sonata.notification.backend.postpone
            # sonata.notification.backend.doctrine
            # sonata.notification.backend.rabbitmq
            backend:              sonata.notification.backend.runtime

            # Example for using RabbitMQ
            #     - { queue: myQueue, recover: true, default: false, routing_key: the_routing_key, dead_letter_exchange: 'my.dead.letter.exchange' }
            #     - { queue: catchall, default: true }
            #
            # Example for using Doctrine
            #     - { queue: sonata_page, types: [sonata.page.create_snapshot, sonata.page.create_snapshots] }
            #     - { queue: catchall, default: true }
            queues:

                # The name of the queue
                queue:                ~ # Required

                # Set the name of the default queue
                default:              false

                # Only used by RabbitMQ
                #
                # Direct exchange with routing_key
                routing_key:          ''

                # Only used by RabbitMQ
                #
                # If set to true, the consumer will respond with a `basic.recover` when an exception occurs,
                # otherwise it will not respond at all and the message will be unacknowledged
                recover:              false

                # Only used by RabbitMQ
                #
                # If is set, failed messages will be rejected and sent to this exchange
                dead_letter_exchange:  null

                # Only used by Doctrine
                #
                # Defines types handled by the message backend
                types:                []
            backends:
                doctrine:
                    message_manager:      sonata.notification.manager.message.default

                    # The max age in seconds
                    max_age:              86400

                    # The delay in microseconds
                    pause:                500000

                    # The number of items on each iteration
                    batch_size:           10

                    # Raising errors level
                    states:
                        in_progress:          10
                        error:                20
                        open:                 100
                        done:                 10000
                rabbitmq:
                    exchange:             ~ # Required
                    connection:
                        host:                 localhost
                        port:                 5672
                        user:                 guest
                        pass:                 guest
                        vhost:                guest
                        console_url:          'http://localhost:55672/api'
                        factory_class:        \Enqueue\AmqpLib\AmqpConnectionFactory
            consumers:

                # If set to true, SwiftMailerConsumer and LoggerConsumer will be registered as services
                register_default:     true

            # Listeners attached to the IterateEvent
            # Iterate event is thrown on each command iteration
            #
            # Iteration listener class must implement Sonata\NotificationBundle\Event\IterationListener
            iteration_listeners:  []
            class:
                message:              Application\Sonata\NotificationBundle\Entity\Message
            admin:
                enabled:              true
                message:
                    class:                Sonata\NotificationBundle\Admin\MessageAdmin
                    controller:           'SonataNotificationBundle:MessageAdmin'
                    translation:          SonataNotificationBundle

    .. code-block:: yaml

        doctrine:
            orm:
                entity_managers:
                    default:
                        mappings:
                            SonataNotificationBundle: ~
                            ApplicationSonataNotificationBundle: ~


Changing AMQP transport
-----------------------

Sonata integrates with `queue interop`_ and by default uses `enqueue/amqp-lib`_.
Though you can pick your favorite library among:

* `enqueue/amqp-ext`_: use `Enqueue\AmqpExt\AmqpConnectionFactory` class.
* `enqueue/amqp-lib`_: use `Enqueue\AmqpLib\AmqpConnectionFactory` class.
* `enqueue/amqp-bunny`_: use `Enqueue\AmqpBunny\AmqpConnectionFactory` class.

For example if you decide to use `enqueue/amqp-bunny`_ transport,
run `composer require enqueue/amqp-ext:^0.8` and change `factory_class` option in the config:

.. configuration-block::

    .. code-block:: yaml

        sonata_notification:
            backends:
                rabbitmq:
                    connection:
                        factory_class:        \Enqueue\AmqpExt\AmqpConnectionFactory



.. _`queue interop`: https://github.com/queue-interop/queue-interop#amqp-interop
.. _`enqueue/amqp-lib`: https://github.com/php-enqueue/enqueue-dev/blob/master/docs/transport/amqp_lib.md
.. _`enqueue/amqp-ext`: https://github.com/php-enqueue/enqueue-dev/blob/master/docs/transport/amqp_ext.md
.. _`enqueue/amqp-bunny`: https://github.com/php-enqueue/enqueue-dev/blob/master/docs/transport/amqp_bunny.md

