# Cli

[![Automation](https://github.com/ghostwriter/cli/actions/workflows/automation.yml/badge.svg)](https://github.com/ghostwriter/cli/actions/workflows/automation.yml)
[![PHP Version](https://badgen.net/packagist/php/ghostwriter/cli?color=777BB4)](https://www.php.net/supported-versions)
[![Packagist Downloads](https://badgen.net/packagist/dt/ghostwriter/cli?color=F28D1A)](https://packagist.org/packages/ghostwriter/cli)
[![PayPal](https://img.shields.io/badge/paypal-@codepoet-0079C1?logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjQiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI%2BPHBhdGggZD0iTTE5LjcxNSA2LjEzM2MuMjQ5LTEuODY2IDAtMy4xMS0uOTk5LTQuMjY2QzE3LjYzNC42MjIgMTUuNzIxIDAgMTMuMzA3IDBINi4yMzVjLS40MTggMC0uOTE2LjQ0NC0xIC44ODlMMi4zMjMgMjAuNjIyYzAgLjM1Ni4yNS44LjY2NS44aDQuMzI4bC0uMjUgMS45NTZjLS4wODQuMzU1LjE2Ni42MjIuNDk4LjYyMmgzLjY2M2MuNDE3IDAgLjgzMi0uMjY3LjkxNS0uNzExdi0uMjY3bC43NDktNC42MjJ2LS4xNzhjLjA4My0uNDQ0LjUtLjguOTE1LS44aC41YzMuNTc4IDAgNi4zMjUtMS41MSA3LjE1Ni01Ljk1NS40MTgtMS44NjcuMjUyLTMuMzc4LS43NDctNC40NDUtLjI1LS4zNTUtLjY2Ni0uNjIyLTEtLjg4OSIgZmlsbD0iIzAwOWNkZSIvPjxwYXRoIGQ9Ik0xOS43MTUgNi4xMzNjLjI0OS0xLjg2NiAwLTMuMTEtLjk5OS00LjI2NkMxNy42MzQuNjIyIDE1LjcyMSAwIDEzLjMwNyAwSDYuMjM1Yy0uNDE4IDAtLjkxNi40NDQtMSAuODg5TDIuMzIzIDIwLjYyMmMwIC4zNTYuMjUuOC42NjUuOGg0LjMyOGwxLjE2NC03LjM3OC0uMDgzLjI2N2MuMDg0LS41MzMuNS0uODg5Ljk5OC0uODg5aDIuMDhjNC4wNzkgMCA3LjI0MS0xLjc3OCA4LjI0LTYuNzU1LS4wODMtLjI2NyAwLS4zNTYgMC0uNTM0IiBmaWxsPSIjMDEyMTY5Ii8%2BPHBhdGggZD0iTTkuNTYzIDYuMTMzYy4wODItLjI2Ni4yNS0uNTMzLjQ5OC0uNzEuMTY2IDAgLjI1LS4wOS40MTYtLjA5aDUuNDk0Yy42NjYgMCAxLjMzLjA5IDEuODMuMTc4LjE2NiAwIC4zMzMgMCAuNDk4LjA4OS4xNjguMDg5LjMzNC4wODkuNDE4LjE3OGguMjVjLjI0OC4wODkuNDk3LjI2Ni43NDguMzU1LjI0OC0xLjg2NiAwLTMuMTEtLjk5OS00LjM1NUMxNy43MTcuNTMzIDE1LjgwNCAwIDEzLjM5IDBINi4yMzVjLS40MTggMC0uOTE2LjM1Ni0xIC44ODlMMi4zMjMgMjAuNjIyYzAgLjM1Ni4yNS44LjY2NS44aDQuMzI4bDEuMTY0LTcuMzc4IDEuMDg0LTcuOTF6IiBmaWxsPSIjMDAzMDg3Ii8%2BPC9zdmc%2B)](https://paypal.me/codepoet)
[![Sponsors via GitHub](https://img.shields.io/github/sponsors/ghostwriter?label=Sponsor+@ghostwriter/cli&logo=GitHub+Sponsors)](https://github.com/sponsors/ghostwriter)

Provides a micro-framework for building CLI tools in PHP.

> [!WARNING]
>
> This project is not finished yet, work in progress.

## Installation

You can install the package via composer:

``` bash
composer require ghostwriter/cli
```

### Star ⭐️ this repo if you find it useful

You can also star (🌟) this repo to find it easier later.

## Usage

```php
<?php declare(strict_types=1);

namespace Vendor\Project;

use GhostWriter\Cli\Attribute\Application;use GhostWriter\Cli\Attribute\Argument;use GhostWriter\Cli\Attribute\Command;use GhostWriter\Cli\Attribute\Option;use Ghostwriter\Cli\Core\MiddlewareInterface;

#[Command(name: 'new', description: 'Create a new project')]
final class NewCommand
{
    #[Argument(help: 'Project name')]
    public string $name;
    #[Option(short: 't', long: 'type', help: 'Project type')]
    public string $type = 'php';
    #[Option(short: 'd', long: 'directory', help: 'Project directory')]
    public string $directory;
}

#[Command(name: 'help', description: 'Show help')]   
final class HelpCommand
{
    #[Argument(help: 'Command to show help for')]
    public ?string $command = null;
}

#[Command(name: 'list', description: 'List all projects')]
final class ListCommand
{
    #[Option(short: 't', long: 'type', help: 'Project type')]
    public string $type = 'php';
}

final readonly class GitHubAuthMiddleware implements MiddlewareInterface
{
    public function __construct(private string $token) {}

    #[Override]
    public function process(CommandInterface $command, HandlerInterface $handler): ResultInterface
    {
        $command->env['GITHUB'] = $this->token;

        return $handler->handle($command);
    }
}

#[Application(
    package: 'ghostwriter/architect',
    commands: [NewCommand::class, HelpCommand::class, ListCommand::class],
    middlewares: [GitHubAuthMiddleware::class]
)]
final class Architect
{
    // Default options available in all subcommands (help, version)
    #[Option(short: 'h', long: 'help', help: 'Show help')]
    public bool $help = false;

    #[Option(short: 'v', long: 'version', help: 'Show version')]
    public bool $version = false;
    
    // Custom optional options (with default values)
    #[Option(short: 'b', long: 'bool', help: 'Set the boolean flag')]
    public bool $bool = false;
    
    #[Option(short: 'f', long: 'float', help: 'Set the float to <float>')]
    public float $float = 0.0;
    
    #[Option(short: 'i', long: 'int', help: 'Set the integer to <int>')]
    public int $int = 0;
    
    #[Option(short: 's', long: 'string', help: 'Set the string to <string>')]
    public string $string = '#BlackLivesMatter';
    
    #[Option(short: 'l', long: 'list', help: 'Set the list to <list>')]
    public array $list = [];
    
    #[Option(short: 'n', long: 'null', help: 'Set the null to <null>')]
    public ?string $null = null;
    
    // Custom required options
    #[Option(short: 'r', long: 'required', help: 'Set the required to <required>', required: true)]
    public string $required;
    
    // Required arguments
    #[Argument(help: 'Project name')]
    public string $project;
    
    // Optional arguments (with default values)
    #[Argument(help: 'Project directory')]
    public string $directory = '.';
}

$exitCode = \GhostWriter\Cli\Application::new(Architect::class)->run($_SERVER['argv']);
exit($exitCode);
```

### Credits

- [Nathanael Esayeas](https://github.com/ghostwriter)
- [All Contributors](https://github.com/ghostwriter/cli/contributors)

### Changelog

Please see [CHANGELOG.md](./CHANGELOG.md) for more information on what has changed recently.

### License

Please see [LICENSE](./LICENSE) for more information on the license that applies to this project.

### Security

Please see [SECURITY.md](./SECURITY.md) for more information on security disclosure process.
