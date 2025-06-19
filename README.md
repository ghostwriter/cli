# Cli

[![GitHub Sponsors](https://img.shields.io/github/sponsors/ghostwriter?label=Sponsor+@ghostwriter/cli&logo=GitHub+Sponsors)](https://github.com/sponsors/ghostwriter)
[![Automation](https://github.com/ghostwriter/cli/actions/workflows/automation.yml/badge.svg)](https://github.com/ghostwriter/cli/actions/workflows/automation.yml)
[![Supported PHP Version](https://badgen.net/packagist/php/ghostwriter/cli?color=8892bf)](https://www.php.net/supported-versions)
[![Downloads](https://badgen.net/packagist/dt/ghostwriter/cli?color=blue)](https://packagist.org/packages/ghostwriter/cli)

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
